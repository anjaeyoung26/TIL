## HTTP

HTTP는 *Hyper Text Transfer Protocol*의 줄임말로, HTML과 같은 하이퍼미디어 문서를 전송하기 위한 [응용 계층](./osi-7-layer.md/#7계층---응용-계층)의 프로토콜이다. 

### **하이퍼미디어란?** 

이전에는 정보를 전달하기 위해서 컴퓨터 디스켓, CR-ROM 등에 저장하여 사용했지만, 모두 오프라인상에서 이루어졌기 때문에 이를 재생하기 위한 매체를 구비해야하는 불편함이 있었다. 하이퍼미디어는 각기 독립된 다양한 매체를 하나의 통합된 매체를 통해 사용자가 필요한 정보를 비선형적으로 선택할 수 있게 한다. 텍스트 위주의 문서가 링크를 통해 연결되어 있는 하이퍼텍스트와 멀티미디어의 개념을 합친 것으로 텍스트 뿐만 아니라 이미지, 그래픽, 사운드, 동영상 등의 다양한 정보를 포함한다.

### WWW

*World Wide Web*라고도 불리는 웹은 1989년 스위스의 CERN 연구소에서 Tim Berhnard Lee가 주도하여 개발한 인터넷 상의 정보 교환을 위한 최초의 하이퍼미디어 시스템이다. 웹은 초기에 사용하기 어려워 많은 이목을 끌지 못했지만, 1993년 GUI 방식의 웹 브라우저인 Mosaic가 개발되면서 급격히 확산되었다. 이후 Internet Explorer 등 상업용 브라우저가 개발되어 많은 사람들이 편리하게 이용할 수 있게 되었다. 웹은 인터넷에서 개별적으로 제공되던 전자우편, FTP, 텔넷과 같은 각종 서비스를 통합하여, 하이퍼미디어를 컴퓨터 상에 실현시켰다. 그리고 웹 브라우저와 웹 서버간의 하이퍼미디어를 전송하기 위해 HTTP 프로토콜을 사용한다.

### 통신 방식

HTTP는 클라이언트가 *HTTP Request*를 보내면 서버는 *HTTP Response*를 응답하는 구조이다. 이처럼 HTTP의 모든 통신은 요청/응답의 형태이다. 또한 HTTP는 Stateless 특성을 가지고 있는데, 이는 상태를 저장하지 않는 것으로 서버는 클라이언트의 요청을 받으면 그에 따른 응답을 할 뿐, 각각의 요청/응답은 독립적이다. 만약 여러 요청/응답 간의 데이터를 공유하기 위해서는 쿠키나 세션을 사용한다. 

&nbsp;
## HTTPS

HTTP는 웹 브라우저와 웹 서버가 데이터를 교환할 때, 누군가가 네트워크 신호를 가로챈다면 교환되는 내용이 노출되는 이슈가 존재한다. 이러한 보안 문제를 해결하고자 HTTPS가 개발되었다. HTTPS는 *Hypter Text Transfer Protocol Secure*의 줄임말로, HTTP에서 데이터 암호화가 추가된 프로토콜이다. HTTPS는 데이터의 암호화/복호화 과정으로 HTTP에 비해 속도가 느리며, 인증서를 발급하고 유지하기 위한 추가적인 비용이 발생하는 단점이 있다. 

또한 HTTPS를 사용함에도 불구하고 '안전하지 않은 사이트'와 같은 알림을 받는 경우가 있다. 이는 신뢰받는 CA 기업이 아닌 자체 인증서를 발급한 경우로, HTTPS를 사용한다고 데이터의 보안이 100% 보장되지 않는다. 하지만 오늘날에는 HTTP와 HTTPS의 속도 차이는 느껴지지 않는 수준이며, 신뢰할 수 있는 CA 기업을 선택한다면 어느정도 보안성을 갖추기 때문에 대부분의 웹 서버는 HTTPS를 사용한다.

### 통신 방식(하이브리드)

1. A 서버에서 공개키와 개인키를 생성하고, CA 기업에게 공개키의 관리를 위한 계약을 한다. CA 기업은 A 서버의 공개키와 공개키 암호화 방법을 담은 인증서를 생성하고, 해당 인증서를 CA 기업의 개인키로 암호화하여 A 서버에게 전달한다.

2. A 서버는 클라이언트가 보낸 요청이 자신의 공개키로 암호화되어 있지 않았다면 클라이언트에게 인증서를 건내준다.(클라이언트가 웹 서버에 처음 접근할 때는 A 서버에 대한 공개키를 가지고 있지 않다.) 이때 CA 기업이 A 서버에게 전달한 인증서를 **SSL 인증서** 라고 한다.

3. 클라이언트는 A 서버가 건내준 인증서를 복호화해야 한다. 하지만 해당 인증서는 CA 기업의 개인키로 암호화되어 있기 때문에 이를 복호화하기 위해서는 CA 기업의 공개키가 필요한데, 클라이언트는 이미 CA 기업의 공개키를 가지고 있다. 왜냐하면 CA 기업은 세계적으로 신뢰성이 검증된 기업으로, 해당 기업의 인증서를 클라이언트가 탐색할 수 있기 때문이다.

4. 클라이언트는 A 서버가 건내준 인증서의 유효성을 검사하고 세션키를 발급한다. 그리고 세션키를 서버의 공개키로 암호화하여 서버로 전달한다. 서버는 자신의 개인키로 복호화하여 세션키를 얻고, 서버와 클라이언트 간에 데이터를 전달할 때 세션키를 사용하여 데이터의 암호화/복호화를 진행한다. 이로써 서버와 클라이언트는 동일한 세션키를 갖는 대칭키 암호화 방식을 사용하게 된다. 

이처럼 서버의 SSL 인증서를 통해 클라이언트와 서버 간의 연결을 수립하는 과정을 ***SSL Handshake***라고 한다. HTTPS는 서버와 클라이언트 간에 처음 연결을 성립할 때 비대칭키를 사용하고, 이후에 발급받은 세션키를 공유하여 대칭키로 사용함으로써 HTTPS는 대칭키 암호화의 장점인 빠른 연산 속도와 비대칭키 암호화의 장점인 안전성을 모두 얻을 수 있다. *SSL Handshake* 이후 세션이 유지되고 있는 동안 세션키를 통해 클라이언트와 서버 간의 데이터를 주고 받는 과정을 *HTTPS 통신* 이라고 하며, HTTPS 통신이 완료되는 시점에서 세션키를 폐기한다.