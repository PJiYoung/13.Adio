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
만약 이렇게 한다면 마지막에 audioRecord의 델리게이트를 설정하는데 AVAudioRecorderDelegate를 상속받지 않아 에러가 발생하게 되는데 이때 에러를 없애기 위해 뷰 컨트롤러의 클래스 선언문에 AVAudioRecorderDelegate의 선언을 추가하면 에러가 사라지게 된다.
- 이 부분은 재생 모드의 초기화에서도 마찬가지로 해주면 된다

**스위치를 통해 녹음 및 재생모드로 변경할 수 있게 하기**
스위치를 On으로 하면 '녹음 모드'가 되고 Off로 하면 '재생 모드가 되어야 하는데 이를 구현하기 위해선 swRecordMode 함수에 코드를 추가해야 한다.
```
// 스위치를 On/Off하여 녹음 모드인지 재생 모드인지를 결정한다.
@IBAction func swRecordMode(_ sender: UISwitch) {
    if sender.isOn {    //녹음 모드일 때 
        audioPlayer.stop.stop()
        audioPlayer.currentTime = 0
        lblRecordTime!.text = convertNSTimeInterval2String(0)
        isRecordMode = true
        btnRecord.isEnabled = true
        lblRecordTime.isEnabled = true
    } else {     //재생 모드일 떄
        isRecordMode = false
        tnRecord.isEnabled = false
        lblRecordTime.isEnabled = false
        lblRecordTime.text = convertNSTimeInterval2String(0)
   }
   selectAudioFile()    // 모드에 따라 오디오 파일을 선택
   //모드에 따라 재생 초기화 또는 녹음 초기화를 수행
   if !isRecordMode {   //녹음 모드가 아닐 때, 즉 재생 모드일 때
      initPlay()
  } else {  //녹음 모드일 때
     initRecord()
  }
}
```
이 코드의 실행되는 순서에 따라 설명하자면,
> 스위치가 [On]이 되었을 경우 '녹음 모드'아무로 오디오 재생을 중지하고 현재 재생 시간을 00:00으로 만들고, isRecordMode의 값을 참(true)으로 설정하고, [Record]버튼과 녹음 시간을 활성화로 설정한다.
> 스위치가 [On]이 아닐 때, 즉 '재생 모드'일 때는 isRecordMode의 값을 거짓(false)으로 설정ㅎ하고, [Record]버튼과 녹음 시간을 비활성화하며, 녹음 시간은 0으로 초기화한다.
> selectAudioFile 함수를 호출하여 오디오 파일을 선택하고, 모드에 따라 초기화 할 함수를 호출한다.

여기까지 하고 결과를 보게 된다면 재생은 문제 없이 진행되면 스위치가 [On]이 되었을때 버튼들이 비활성화되고 현재 재생 시간과 총 재생 시간이 모두 '00:00'으로 초기화 되어 [Record]버튼과 녹음 시간이 활성화된다.

- 이 앱을 다 만들어 결과를 보게 된다면 재생은 문제 없이 진행된다. 스위치가 [On]이 되면 총 재생 시간이 '00:00'으로 초기화되고 [Record]버튼을 클릭하면 녹음이 시작된다. 녹음이 진행되는 동안 녹음 시간도 변경되게 된다. [Stop]버튼을 누르면 버튼 이름이 'Record'로 변경되고 재생 시간도 변경되는 것을 확인할 수 있다. 그리고 녹음된 파일로 [Play], [Pause], [Stop]도 제대로 작동된다. 스위치가 [Off]가 되면 재생 시간이 원래 음악의 전체 시간으로 바뀌면서 음익이 선택되고 이 파일로 [Play], [Pause], [Stop]버튼도 제대로 작동된다.
