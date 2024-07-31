## Package.resolved file is corrupted or malformed

프로젝트의 의존성 버전을 확인하는 과정에서 문제가 발생한 것으로, Package.resolved 파일을 직접 삭제해서 해결해야 한다.

1. 프로젝트 폴더로 이동
2. .xcodeproj 혹은 .xcworkspace 마우스 오른쪽 클릭
3. 패키지 내용 보기 클릭
4. Package.resolved 파일 삭제
5. Xcode Clean Build Folder
6. 빌드

만약 Package.resolved 파일이 보이지 않으면 Shift + Cmd + .으로 숨겨진 파일 및 폴더를 표시하면 된다.