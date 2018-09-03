# Make android media player service

## Structure

안드로이드 MediaPlayer는 두 가지 구조로 이루어져 있다.

하나는 실제로 백그라운드에서 Media를 재생시키는 MediaBrowserService 클래스이고,
다른 하나는 UI와 MediaBrowserService를 연결하는 역할을 담당하는 MediaBrowser 클래스이다.

이 둘의 관계는 오디오와 오디오를 작동시키는 여러가지 인터페이스 (재생, 멈춤 버튼 등)의 관계와 유사하다.

## MediaBrowserService

MediaBrowserService의 구현은 
- Media의 여러가지 정보를 담고 있는 MediaSession과 MediaSession 관련 이벤트에 트리거되는 MediaSessionCallback
- 실제 음악의 재생을 담당하는 MediaPlayer (혹은 ExoPlayer)
- Media의 재생 상태를 관리하는 PlaybackState

이 세 가지의 조합으로 이루어진다.

## MediaBrowser

MediaPlayer의 UI(액티비티)와 Service를 연결하는 다리 역할을 하는 클래스이며,

Connection/Subscription/Controller Callback의 세 가지 콜백 클래스를 활용해서 

MediaItem 로드, UI 연결(버튼에 기능 연결), 서비스 종료 등의 상황을 처리한다.

핵심은 모든 이벤트가 별개의 콜백을 통해 처리된다는 것.
