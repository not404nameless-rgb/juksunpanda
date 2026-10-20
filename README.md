# CleanDL — 광고 없는 개인용 다운로더 (하늘~파랑 테마)

죽순판다는 영상·음원을 편하게 내려받는 프로그램입니다. 하늘색 → 파란색 그라데이션 배경에
둥근 카드형 UI로 디자인했습니다.

**이 프로그램이 절대 하지 않는 것**
- 광고 표시 (새 탭, 팝업 등)
- 브라우저 즐겨찾기 / 홈페이지 / 새 탭 변경
- 시작프로그램 등록
- 작업 스케줄러(예약 작업) 등록
- 어떤 부가 프로그램도 함께 설치하지 않음

내부적으로는 오픈소스 도구인 **yt-dlp**를 그대로 사용합니다.

---

## 1. 실행 (파이썬 이미 있는 경우)

```
python -m pip install -r requirements.txt
python cleandl.py
```

또는 `setup.bat` → `run.bat` 순서로 더블클릭.

**ffmpeg가 없다면**: https://www.gyan.dev/ffmpeg/builds/ 에서 "release full" 다운로드 →
압축 풀고 안의 `ffmpeg.exe`를 `cleandl.py`가 있는 폴더에 그대로 복사해 넣으면 됩니다.
(PATH를 따로 건드릴 필요 없이 프로그램이 같은 폴더의 ffmpeg를 자동으로 찾습니다.)

---

## 2. 완전한 exe 파일로 만들기

### 방법 A — 자동 스크립트 (추천)

이 폴더에서 **`build_exe.bat`을 더블클릭**하면 끝입니다.
- pyinstaller / yt-dlp가 없으면 자동 설치
- 폴더 안에 `ffmpeg.exe`가 있으면 exe 안에 자동으로 함께 포함
- 폴더 안에 `icon.ico`가 있으면 그 아이콘을 자동 적용
- 완료되면 `dist\CleanDL.exe` 생성
- **성공/실패를 명확히 알려줍니다.** 실패하면 같은 폴더에 생기는
  `build_log.txt`에 전체 로그가 저장되고, 창에도 마지막 25줄이 바로 보입니다.
  실패했다면 그 내용을 알려주시면 원인을 정확히 짚어드릴 수 있어요.

### 방법 B — 직접 명령어로 만들기 (원리를 알고 싶다면)

명령 프롬프트(cmd)를 이 폴더에서 열고:

**1) pyinstaller 설치**
```
pip install pyinstaller
```

**2) 기본 빌드**
```
pyinstaller --onefile --windowed --name CleanDL cleandl.py
```

각 옵션의 의미:
| 옵션 | 의미 |
|---|---|
| `--onefile` | 결과물을 exe 파일 하나로 묶음 (실행 시 내부적으로 임시 폴더에 압축 해제) |
| `--windowed` | 실행할 때 검은 콘솔창이 뜨지 않게 함 (GUI 프로그램이므로 필수) |
| `--name CleanDL` | 결과물 이름을 `CleanDL.exe`로 지정 |

빌드가 끝나면 `dist` 폴더 안에 `CleanDL.exe`가 생깁니다. (`build`, `CleanDL.spec`은 빌드 중간 산출물이라 지워도 됩니다)

**3) (선택) ffmpeg를 exe 안에 함께 포함시키기**

exe 하나만 옮겨도 바로 동작하게 하고 싶다면, ffmpeg.exe를 이 폴더에 두고:
```
pyinstaller --onefile --windowed --name CleanDL --add-binary "ffmpeg.exe;." cleandl.py
```
`downloader.py`의 자동 감지 로직이 exe 실행 시 내부 임시 폴더(`sys._MEIPASS`)에서
ffmpeg를 찾아 자동으로 사용합니다. (한 파일로 완전히 독립적인 exe 완성)

**4) (선택) 아이콘 적용**

원하는 이미지를 `.ico` 파일로 변환(예: https://icoconvert.com , 또는 그림판→저장 후
온라인 변환기 이용)한 뒤 `icon.ico`로 이름 지어 폴더에 넣고:
```
pyinstaller --onefile --windowed --name CleanDL --icon=icon.ico cleandl.py
```

**5) onefile vs onedir**

`--onefile` 대신 `--onedir`(기본값, 옵션 생략 시)로 빌드하면 실행 속도가 더 빠르고
백신 오탐(아래 참고)도 덜하지만, 결과물이 폴더 형태(`dist\CleanDL\` 안에 exe + 여러 파일)가
됩니다. exe 하나만 깔끔하게 들고 다니고 싶으면 `--onefile`, 실행 속도가 더 중요하면
`--onedir`을 쓰세요.

### 참고: 백신(Windows Defender 등) 오탐

PyInstaller로 만든 `--onefile` exe는 압축을 해제하는 방식 때문에 가끔 백신이
"의심스러운 파일"로 잘못 표시하는 경우가 있습니다(코드 자체와 무관한 알려진 현상입니다).
이런 경우:
- `--onedir`로 다시 빌드해보거나
- Windows 보안 > 바이러스 및 위협 방지 > 제외 항목에 해당 exe를 추가하면 됩니다.

---

## 3. 사용법

1. 영상 주소 입력 (붙여넣기 버튼 사용 가능)
2. **동영상(mp4)** / **음원만(mp3)** 중 선택
3. 화질 선택 (최고 / 1080p / 720p / 480p)
4. 구간만 받고 싶으면 스위치를 켜고 시작/종료 시간 입력
   - 형식: `90` (초) / `1:30` (분:초) / `1:02:30` (시:분:초)
5. 저장 위치 선택 (기본값: 다운로드 폴더)
6. **다운로드 시작** 클릭 → 로그창에서 진행 상황 확인

---

## 4. 문제 해결

| 증상 | 해결 |
|---|---|
| "ffmpeg를 찾을 수 없습니다" 오류 | ffmpeg.exe를 cleandl.py(또는 CleanDL.exe)와 같은 폴더에 복사 |
| 다운로드가 아예 안 됨 / "오류" 로그 | 영상 주소 확인, 비공개/연령제한 영상은 다운로드 불가 |
| exe 실행이 느림 | `--onefile`의 압축 해제 특성 때문. `--onedir`로 다시 빌드하면 빨라짐 |
| 백신이 exe를 차단함 | 위 "백신 오탐" 항목 참고 |
| `build_exe.bat`이 "실패"라고 뜸 | `build_log.txt` 맨 아래 오류 메시지 확인. 아주 최신 Python 버전(예: 3.14 갓 나온 버전)을 쓰는 경우 일부 패키지가 아직 해당 버전용 설치 파일을 제공하지 않아 설치 자체가 실패할 수 있습니다 — 이 경우 python.org에서 3.12나 3.13처럼 조금 더 안정화된 버전을 추가로 설치해 그 버전으로 빌드하면 대부분 해결됩니다 |

---

## 참고

- 개인 시청/소장 용도로만 사용하세요. 다운로드한 영상 재배포는 저작권법 위반이 될 수 있습니다.
- 이 프로그램은 어떤 형태로도 사용 기록을 수집하거나 외부로 전송하지 않습니다.
