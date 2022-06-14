# 13.Adio
## 음악 재생하고 녹음하기

오디오를 재생하는 방법 중 가장 쉬운 방법은 AVAudioPlayer을 사용하는 방법이 있다.
오디오 재생 앱은 다양한 형태로 만들 수 있지만 이 앱은 가장 기본적인 기능을 수행하는 형태의 앱이다.

음악이 재생되고 녹음이 되기 위해서는 녹음 모드와 재생 모드가 있어야하고 녹음과 재생을 하고 난 후 초기화도 되야하며, 버튼이 눌렸을 경우 활성화 또는 비활성화가 되어야 한다. 
또한 재생시간도 표시되어야 하며 버튼을 눌렀을때 동작하는 부분 등도 나타나야한다.

**이 앱에 쓰일 변수와 상수를 선언하고 그 의미 적어보기**
```
var audioPlayer : AVAudioPlayer! // AVAudioPlayer 인스턴스 변수
var audioFile : URL! //재생할 오디오의 파일명 변수
let MAX_VOLUME : Float = 10.0 //최대 볼륨, 실수형 상수
var progressTimer : Timer! // 타이머를 위한 변수
```
이렇게 적어볼 수 있다.

**재생 모드일 때, 녹음 모드일 때를 나타낼땐 if - else 문으로**
```
override func viewDidLoad() {
    super.viewDidLoad()
   // Do any additional setup after loading the view, typically from a nib.
   selectAudioFile()
   if !isRecordMode { // 재생 모드일 때
      initPlay()
      btnRecord.isEnabled = false
      lblRecordTime.isEnabled= false
   } else { // 녹음 모드일 때
     initRecord()
   }
 }
 ```
 > if문의 조건이 '!isRecordMode'이다. 이는 '녹음 모드가 아니라면'이므로 재생 모드를 말하는 것이다. 따라서 initPlay함수를 호출하게 된다. 
 > 조건이 해당하는 것이 재생 모드이므로 [Record]버튼과 재생 시간은 비활성화로 설정한다.
 > else문은 조건에 해당하지 않는 경우, 이는 '녹음 모드라면'이므로 initRecord함수를 호출하게 된다.

녹음 모드의 초기화를 할댄 initRecord함수에 코드를 추가하여 녹음에 대한 설정을 하는것이여서 따로 설명을 하진 않겠다.
initRecord함수에 코드를 추가하여 selectAudioFile 함수에서 audioFileㅇㄹ URL로 하는 audioRecorder 인스턴스를 생성하는데 이때 do-try-catch문을 사용한다.
```
func initRecord() { 
  .
  .
    do {
        audioRecorder = try AVAudioRecorder(url: audioFile, settings:
             recordSettings)
    } catch let error as NSError {
        print("Error-inirRecord : \(error)")
    }
    
    audioRecorder.delegate = self
 }
 ```

