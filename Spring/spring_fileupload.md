
![author](https://img.shields.io/badge/author-daesungRa-lightgray.svg?style=flat-square)
![date](https://img.shields.io/badge/date-190209-lightgray.svg?style=flat-square)

## **Spring** 컨테이너 내장 인터페이스인 **MultipartHttpServletRequest** 활용해 파일 업로드하기

- 기존 서블릿 컨테이너에서는 **request** 객체를 **MultipartRequest** 인터페이스로 변환해 자동 파일 업로드 및 오리지널, 시스템 파일명 분리까지 가능했다
- 마찬가지로 **MultipartHttpServletRequest** 인터페이스도 파일업로드가 가능하지만, 실제 저장되는 시스템 파일을 자동으로 생성하지는 못한다 (방법을 못찾았을 수도 있음)
- 요청 객체 변환의 전 과정은, 자바 언어의 특성인 상속과 인터페이스 구현 기능으로 편리하게 가능하다
- 본 포스트에서는 회원가입 로직에서 멤버이미지를 업로드하는 예제를 활용해 파일 업로드 기능을  공부한다

### 시나리오

1. 브라우저 화면의 폼 태그에는 **enctype='multipart/form-data'** 속성이 적용되어 있어야 한다 (이후 정보는 일반 **request** 객체로는 다루지 못하게 됨)
2. 브라우저가 요청한 **request** 정보를 상위 인터페이스 중 하나인 **MultipartHttpServletRequest** 로 변환
	* 관련 인터페이스들은 **spring** 에 포함된 클래스들이다
	* **MultipartHttpServletRequest** 는 **HttpServletRequest, MultipartRequest, ServletRequest** 의 자손 인터페이스이다
	* 상속 및 인터페이스 구현 원리에 따라 **request** 객체는 각 인터페이스 변수에 담아 유연하게 사용 가능하다
	* 이 인터페이스 형식으로 변환될 때 컨테이너의 임시 저장소에 **request** 내 파일이 업로드된다. 이 정보는 실제 저장경로에 **transfer** 하지 않으면 곧 휘발된다
3. 변환한 파일 정보가 존재하는지 반복자 (**Iterator**) 로 확인한 후, 저장 파일 정보를 **MultipartFile** 형식으로 추출한다 (**mFile** 로 가칭)
4. **mFile.getOriginalFilename()** 으로 실제 저장된 파일 정보를 확인한 후, 그것을 데이터베이스에 담기 위해 활용한다 (**oriFileName** 가칭)
5. 서버 내 실제 저장 경로에 동일한 파일명이 존재하는지 **'[경로] + oriFileName'** 으로 확인한 후, 존재하지 않는다면 동일한 이름의 **sysFileName** 을 생성하고, 존재한다면 **substring** 으로 시스템 시각을 덧붙여 **sysFileName** 을 생성한다
6. 생성된 sysFileName 을 기준으로 임시 업로드된 파일정보를 실제 저장 경로로 transfer 시킨다 (**mFile.transferTo([저장경로] + sysFileName)**).
7. 마지막으로 각 **oriFileName** 과 **sysFileName** 을 데이터베이스에 저장한다. 각각은 사용자에게 보여질 원본파일명과 실제 서버에 저장될 유일한 파일명을 의미한다

### **MultipartHttpServletRequest** 활용을 위한 스프링 환경설정

- **pom.xml** 에 **commons-fileupload** 와 **commons-io** 를 위한 **dependency** 를 추가한다 (메이븐 레포에서 적절한 버전 검색)

```XML
	...

	<!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
	<dependency>
		<groupId>commons-fileupload</groupId>
		<artifactId>commons-fileupload</artifactId>
		<version>1.4</version>
	</dependency>
		<!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
	<dependency>
		<groupId>commons-io</groupId>
		<artifactId>commons-io</artifactId>
		<version>2.6</version>
	</dependency>

	...
```

- **servlet-context.xml** 에서 **multipartResolver beans** 를 추가한다
- 이것은 **MultipartHttpServletRequest** 를 위한 빈 객체로 활용된다

```XML
	...

	<!-- Resolves upload files -->
	<beans:bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
		<!-- maxSize = 10mb -->
		<beans:property name="maxUploadSize" value="100000000"></beans:property>
		<beans:property name="defaultEncoding" value="utf-8"></beans:property>
	</beans:bean>

	...
```

- 프로퍼티로는 업로드 파일 사이즈를 지정하는 **maxUploadSize (10mb)** 속성과 **defaultEncoding (utf-8)** 속성을 지정한다
- 이제 **MultipartHttpServletRequest** 를 임포트해서 사용 가능하다

### 코드

- 회원등록 시 **MEMBER** 테이블에 **insert** 하기 위한 **MemberVo** 객체를 반환하는 **FileUpload** 클래스를 작성한다

FileUpload.java
```JAVA
package com.daesungra.model;

import java.io.File;
import java.util.Iterator;

import javax.servlet.http.HttpServletRequest;

import org.springframework.stereotype.Repository;
import org.springframework.web.multipart.MultipartFile;
import org.springframework.web.multipart.MultipartHttpServletRequest;

/*
 * 파일 업로드를 위한 멀티파트 객체 활용
 * 반환값은 각 요청 형식에 맞는 vo 객체이다
 */
@Repository
public class FileUpload {
	private MemberVo mvo = null;

	private String oriFileName = "";
	private String sysFileName = "";
	private String saveDir = "D:/git/DeskTop-portfolio-daesungra/src/main/webapp/resources/imgs/memberImg/";
	
	// 전달받은 요청 객체의 파일을 multipart 인스턴스를 활용해 서버에 저장한 후 (sysFileName)
	// 오리지널 파일명, 실제 저장 파일명 구분해 vo 객체 생성 후 반환
	public MemberVo getMemberVo (HttpServletRequest request) {		
		// 지정된 경로의 디렉토리가 없다면 새로 생성
		File file = new File(saveDir);
		if (!file.isDirectory()) {
			file.mkdirs();
		}
		
		try {
			// enctype='multipart/form-data' 방식으로 넘어온 요청정보를 다루기 위해 
			// 스프링 컨테이너에 포함된 MultipartHttpServletRequest 객체로 변환하여 사용한다
			// 이 클래스는 HttpServletRequest, MultipartRequest, ServletRequest 의 세 인터페이스를 구현한 자손 인터페이스이므로
			// 같은 계열 request 객체를 캐스팅해 활용 가능하다
			MultipartHttpServletRequest multi = (MultipartHttpServletRequest) request;
			Iterator<String> ite = multi.getFileNames();
			while (ite.hasNext()) {
				String tagName = ite.next();
				MultipartFile mFile = multi.getFile(tagName);
				
				// 전달받은 파일 확인
				System.out.println("file form name : " + mFile.getName());
				System.out.println("ori file name : " + mFile.getOriginalFilename());
				System.out.println("file size : " + mFile.getSize());
				System.out.println("is existing? : " + mFile.isEmpty());
				
				// 오리지널 파일명, 인코딩 처리
				oriFileName = new String(mFile.getOriginalFilename().getBytes("8859_1"), "utf-8");
				// 실제 저장 파일명은 오리지널 파일명으로 지정했다가, 추후 지정 경로에 동일 파일명이 존재하면 수정한다
				sysFileName = oriFileName;
				
				// 지정 경로에 파일 저장하기
				// 저장할 파일이 존재한다면 수행
				if (sysFileName != null && !sysFileName.equals("")) {
					// 지정 경로에 동일 파일명이 이미 존재한다면 실제 저장 파일명 변경 (시스템 시각을 더함)
					if (new File(saveDir + sysFileName).exists()) {
						String preName = sysFileName.substring(0, sysFileName.lastIndexOf("."));
						String postName = sysFileName.substring(sysFileName.lastIndexOf("."), sysFileName.length());
						sysFileName = preName + "_" + System.currentTimeMillis() + postName;
					}
					
					// 컨테이너에 임시 파일로 저장되어 있으므로, 실제 지정 경로로 transfer 해야 한다
					// InputStream 사용해도 됨
					mFile.transferTo(new File(saveDir + sysFileName));
					System.out.println("================================");
					System.out.println("[result] ori : " + oriFileName);
					System.out.println("[result] sys : " + sysFileName);
					
					// 파일 저장까지 완료했다면, 반환할 vo 객체를 생성한다
					// 여기에 ori, sys 파일을 구분해서 세팅한다
					this.mvo = new MemberVo();
					
					mvo.setUserId(multi.getParameter("userId"));
					mvo.setUserPwd(multi.getParameter("userPwd"));
					mvo.setUserName(multi.getParameter("userName"));
					mvo.setEmail(multi.getParameter("email"));
					mvo.setPhone(multi.getParameter("phone"));
					mvo.setPostal(multi.getParameter("postal"));
					// 추가주소가 있다면,
					if (!multi.getParameter("addressAdd").equals("")) {
						mvo.setAddress(multi.getParameter("address") + " " + multi.getParameter("addressAdd"));
					} else {
						mvo.setAddress(multi.getParameter("address"));
					}
					mvo.setPhoto(saveDir + sysFileName); // 실제 저장 파일명. 나중에 쉽게 가져오기 위해 전체 경로 입력
					mvo.setPhotoOri(oriFileName); // 사용자에게 보여질 오리지널 파일명
				} else {
					// 저장에 성공한 파일이 존재하지 않거나
					// 애초에 요청받은 파일 정보가 없는 경우 파일을 제외한 정보만 저장됨
					this.mvo = new MemberVo();
					
					mvo.setUserId(multi.getParameter("userId"));
					mvo.setUserPwd(multi.getParameter("userPwd"));
					mvo.setUserName(multi.getParameter("userName"));
					mvo.setEmail(multi.getParameter("email"));
					mvo.setPhone(multi.getParameter("phone"));
					mvo.setPostal(multi.getParameter("postal"));
					// 추가주소가 있다면,
					if (!multi.getParameter("addressAdd").equals("")) {
						mvo.setAddress(multi.getParameter("address") + " " + multi.getParameter("addressAdd"));
					} else {
						mvo.setAddress(multi.getParameter("address"));
					}
				}
			}
		} catch (Exception ex) {
			ex.printStackTrace();
		}
		
		return this.mvo;
	}
}
```
- 컨트롤러는 **FileUpload** 인스턴스를 만들어 요청받은 **request** 객체를 넘겨주고 **MemberVo** 객체를 리턴받는다
- **FileUpload** 에서는 파일 업로드 로직이 실행되고, 성공했다면 **oriFileName** 과 **sysFileName** 을 포함한 적절한 **vo** 객체를 만들어 반환한다 (최종 회원정보를 DB 에 저장하기 위해)

join result - directory
![join result - directory](https://github.com/daesungRa/MyStudy/blob/master/imgs/SpringStudy/joinresult_directory01.png)
join result - db
![join result - db](https://github.com/daesungRa/MyStudy/blob/master/imgs/SpringStudy/joinresult_db01.png)

- 파일업로드가 되지 않거나 업로드 결과가 없거나 에러 발생 시 **null** 이 반환되며, 애초에 요청 파일 자체가 없다면 파일정보를 제외한 **vo** 객체를 만들어 반환한다(요청정보 필터링은 브라우저에서 처리한 후 전송됨)
- 파일 업로드를 한번 수행한 후, 서버 저장 경로에 중복된 파일명이 존재하므로 **sys** 파일명 중간에 시스템 시각이 붙도록 조치되었고(**directory**), 사용자에게 보여질 **ori** 파일명은 그대로인 것을 볼수 있다(**db**)
- 추후 **FileUpload** 클래스에서는 웹사이트의 각 기능에 맞는 **vo** 를 생성 및 반환하는 메서드를 구현할 것







