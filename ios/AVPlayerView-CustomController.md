# AVPlayerViewController

 * AVPlayer
    * play상태 delegate 없음. notification사용해야 함.

 * Custom Controller
    * 방법 1
        * AVPlayerViewController에 customControllerView 추가.
        * 크기변경이 커스텀하게 안됨 (기본 플레이어처럼만 플레이어 크기 변경된다. 기본/전체화면) - gravity로 변경
        * 화면 회전 시 추가한 뷰는 오토레이아웃 안됨. 다시 맞춰주어야 함. (매우 불편)
        * controllerView 추가 했을 때 플레이어 아래로 들어감/ 클릭액션 플레이어가 가져감
            * 해결 방법: 
                > UIApplication.shared.keyWindow?.addSubview(customview)

                > // 이 방법은 써봤는데 안됨
                > //        self.view.addSubview(customview)
                > //        self.view.bringSubview(toFront: btnPlay)

        * 플레이어 크기변경 안되는 게 치명적이라 이 방법은 안쓰기로 함.

    * 방법 2
        * 기본 ViewController 에 ContainerView-AvPlayerview 추가해서 사용
        * AVPlayerViewController 사이즈 조절 시 애니메이션 추가했을 때, 영상 부분에는 애니메이션이 듣지 않음. 곤란하군.
            * 해결방안 생각 1. gravity 설정 resize로 변경 후, viewcontroller size/ 위치 변경 - 크기변경시 에니메이션 듣지 않음 뷰 이동은 해결됨
            * 해결방안 생각 2. containerView 대신 avplayerlayer사용.  - 애니메이션이 들어가지긴 하는데 속도는 조절 안됨
            * 일단 - 해결방안 1, 2의 경우 비디오 비율이 일정하지 않아서 어려움. 눈치채지못하도록 애니메이션 속도를 매우 빠르게 해보자.
                    
        * ContainerView 특징인건지는 아직 잘 모르겠지만, AVPlayerView에대한 탭 액션이 잡히지 않음. 위에 다른 뷰를 씌어서 해야함.
        * constainerViwe 의 부모 컨트롤러 종료 시, view는 종료되지만 player sounds가 계속 출력되는 현상 있음.
            * 플레이어초기화 시켜주어야 함.
             ~~~
             player?.player?.pause()
             player?.playerLayer?.removeFromSuperlayer()
             player?.player = nil
             ~~~

    * 방법 3
        * 방법2- 해결방안 생각 2 의 방법임. ContainerView 대신에 AVPlayerLayer를 UIView에 추가해서 사용.
        * 이건 영상 재생 중, seek할 때 화면 보여주고 싶은 경우애도 사용할 수 있을 것 같다. - 유튜브처럼
        * ~~~
            let playerItem = AVPlayerItem.init(url: path)
            let avplayer = AVPlayer.init(playerItem: playerItem)
            playerLayer = AVPlayerLayer.init(player: avplayer)
            playerLayer?.frame = CGRect(x: view.frame.origin.x , y: view.frame.origin.y, width: view.frame.width, height: view.frame.height)
            playerLayer?.videoGravity = AVLayerVideoGravity.resize
            view.layer.addSublayer(playerLayer!)
            avplayer.play()
            



# ETC

 * CMTIme
    : CMTime은 시간 값을 나타내는 구조체 타입입니다. CMTime은 유리수로 표현되며 CMTime을 표현하기 위해서는 분자와 분모에 해당하는 값을 필요로 합니다.
    * let time1 = CMTimeMake(1,4) // 1/4
        let time2 = CMTimeMake(2,8) // 2/8
        let time3 = CMTimeMake(1,10000) // 1/10000

        CMTime구조체는 CMTimeMake라는 함수로 생성할 수 있습니다.



        time1은 value는 1이고 timescale은 4로 1/4초를 의미한다.
        time2는 value는 2이고 timescale은 8로 2/8초를 의미한다.
        time3는 value는 1이고 timescale은 10000으로 1/10000초를 의미한다.
        즉 time1과 time2는 같은 값이다. (CMTimeCompare(time1, time2) = 0)
        CMTime은 비디오와 오디오 같은 미디어의 타임라인을 위해 고안된 타입이라고 애플의 문서에 나와있습니다. 그러므로 비디오와 오디오와 같은 미디어를 다루는 작업을 해야 한다면 CMTime을 자주 보게 될 것 같습니다.

    * 출처: <http://baked-corn.tistory.com/95>

 * 볼륨조절을 커스톰해서 화면 오른쪽 세로 방향 스와이프시 조절되도록 했음 (버튼눌렀을 때도 해당 커스텀뷰 출력 - 노티피케이션 사용): 그랬더니 스와이프 빠르게했을때 노티가 뒤로 쌓여 실행됨.
    * %5 로 소리조절 되도록하니 수정됨.
    * slider자체 커스텀해도 될듯.
    * 볼륨조절 화면 안보이게 하려면 무조건 addsubview로 volumeview 추가해야함.
        ~~~
        let frameVolume = CGRect(x: -1000 , y: -1000, width: 0, height: 0)
        let viewVolume = MPVolumeView(frame: frameVolume)
        ~~~
* 아이폰 X 가로모드에서는 상태바 안나옴.