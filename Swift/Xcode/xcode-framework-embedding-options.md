# Xcode 프레임워크 임베딩 옵션

<p align="center">
<img src="https://github.com/anjaeyoung26/private-til/assets/61190690/177e3d1c-a51b-4372-9898-3319e058f22c">
</p>

## Do Not Embed

애플리케이션 패키지에 모든 프레임워크 코드가 포함된 **Frameworks 폴더를 포함시키지 않는** 옵션이다. **정적 프레임워크**는 애플리케이션 실행 파일에 프레임워크 컨텐츠가 포함되어 있어서 Do Not Embed 옵션으로 설정한다.

## Embed & Sign

애플리케이션 패키지에 모든 프레임워크 코드가 포함된 **Frameworks 폴더를 포함하는** 옵션이다. **동적 프레임워크**는 런타임에 프레임워크가 링킹되고 애플리케이션 패키지에 추가하므로 Embed & Sign 옵션으로 설정한다.

## Embed Without Signing

프레임워크가 이미 서명이 완료된 경우 설정한다. (일반적으로 Embed & Sign 사용)