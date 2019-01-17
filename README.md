:kr:욜로용 딱지 표식하기  

출처:  
  * https://github.com/AlexeyAB/Yolo_mark

---
## 욜로용 딱지 표식하기

욜로-v3(yolo-v3)과 욜로-v2(yolo-v2) 벼림을 위해 이미지에 개체의 경계상자를 표식하기 위한 윈도우와 리눅스 GUI 도구

### 1. 컴파일하는 방법

  * 윈도우에서 컴파일 한다면 MSVS2013/2015 에서 `yolo_mark.sln`을 연다, **x64** 와 **Release** 로 컴파일한다 그리고 다음파일을 실행한다: `x64/Release/yolo_mark.cmd`

  * 컴파일 전에 `yolo_mark.sln`에서 자신의 컴퓨터에 설치된 OpenCV 2.x/3.x 로 경로를 변경한다:

    - 프로젝트 -> 속성 -> C/C++ -> 일반 -> 추가 포함 디렉토리에 추가: `C:\opencv_3.0\opencv\build\include;`
    - 프로젝트 -> 속성 -> 링커 -> 일반 -> 추가 라이브러리 디렉토리에 추가: `C:\opencv_3.0\opencv\build\x64\vc14\lib;`

  * 리눅스에서 컴파일 한다면 콘솔에서 3개의 명령을 친다:

```
  cmake .
  make
  ./linux_mark.sh
```

모두 지원됨: OpenCV 2.x 와 OpenCV 3.x

---
### 2. 딱지 붙이는 방법

1. 평가하기 위해, 간단히 실행한다

  * 윈도우에서: `x64/Release/yolo_mark.cmd`
  * 리눅스에서: `./linux_mark.sh`

2. 자신의 맞춤이미지 딱지붙이기를 위해 사용한다면:

  * `x64/Release/data/img`폴더에서 모든파일을 지운다
  * `x64/Release/data/img`폴더에 자신의 `.jpg`파일을 넣는다
  * `x64/Release/data/obj.data`파일에서 분류개수(검출을 위한 개체)를 변경한다:  
    https://github.com/AlexeyAB/Yolo_mark/blob/master/x64/Release/data/obj.data#L1 (1행)
  * `x64/Release/data/obj.names`파일에 개체이름을 넣는다, 각 행에 하나씩:  
    https://github.com/AlexeyAB/Yolo_mark/blob/master/x64/Release/data/obj.names
  * 파일을 실행한다: `x64\Release\yolo_mark.cmd`

3. 자신의 맞춤개체에 대해 벼림을 한다면, `x64/Release/yolo-obj.cfg`파일에서 행 2개를 변경해야한다

  * 분류개수(개체) 설정:  
    https://github.com/AlexeyAB/Yolo_mark/blob/master/x64/Release/yolo-obj.cfg#L230 (230행)
  * `filter`값 설정:  
    - 욜로 v2는 `(classes + 5)*5` 값으로  
    - 욜로 32는 `(classes + 5)*3` 값으로  
    https://github.com/AlexeyAB/Yolo_mark/blob/master/x64/Release/yolo-obj.cfg#L224 (224행)

  3-1 나선단에 대한 미리벼림된 가중값(76 MB)을 내려받는다:  
  http://pjreddie.com/media/files/darknet19_448.conv.23

  3-2 파일을 넣는다: `darknet` 실행가능파일 가까이 `yolo-obj.cfg`, `data/train.txt`, `data/obj.names`, `data/obj.data`, `darknet19_448.conv.23`파일과 `data/img` 폴더를,

  3-3 그리고 벼림시작:  
  `darknet detector train data/obj.data yolo-obj.cfg darknet19_448.conv.23`

 더 자세한 설명은 [:kr:여기](https://github.com/zeuseyera/darknet-kr/SaYongBeob.md)[(영문)](https://github.com/AlexeyAB/darknet#how-to-train-to-detect-your-custom-objects)를 봐라.

---
### 3. 동영상파일에서 장면을 가져오는 방법

동영상파일에서 장면을 가져오기 위해(N 장면마다 저장한다, 본보기에서 N=10), 이 명령을 사용할수 있다:

  * 윈도우에서: `yolo_mark.exe data/img cap_video test.mp4 10`
  * 리눅스에서: `./yolo_mark x64/Release/data/img cap_video test.mp4 10`

`data/img`폴더는 이전에 생성되어야 한다. 또한 윈도우에서, `opencv\build\bin`로 부터 `opencv_ffmpeg340_64.dll`파일을 `yolo_mark.exe` 가까이 두어야 한다.

결과적으로, `data/img`폴더에 많은 장면이 수집된다. 그런다음 이러한 명령을 사용하여 수동으로 딱지붙이기를 할수 있다:

  * 윈도우에서: `yolo_mark.exe data/img data/train.txt data/obj.names`
  * 리눅스에서: `./yolo_mark x64/Release/data/img x64/Release/data/train.txt x64/Release/data/obj.names`

---
### 4. 포함된 파일의 용도

  * `x64/Release` 폴더에 있는 파일
    - yolo_mark.cmd - `yolo_mark` 사용방법 본보기:  
    `yolo_mark.exe data/img data/train.txt data/obj.names`
    - train_obj.cmd - 자신의 맞춤개체에 대한 욜로 버림방법(이 파일을 `darknet.exe`가까이 넣는다) 본보기:  
    `darknet.exe detector train data/obj.data yolo-obj.cfg darknet19_448.conv.23`
    - yolo-obj.cfg - 2 개체에 대한 yoloV3 신경망 본보기


  * `x64/Release/data` 폴더에 있는 파일
    - obj.names - 개체이름을 가진 목록의 본보기
    - obj.data - 욜로 v3 벼림을 위한 구성을 가짐 본보기
    - train.txt - 욜로 v3 벼림을 위한  이미지 파일이름의 목록을 가진 본보기


  * `x64/Release/data/img`폴더의 `air4.txt` - 비행기가 포함된 air4.jpg 이미지상의 개체(비행기 이미지)의 좌표를 가진다(class=0)





---
