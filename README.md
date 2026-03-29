# 🍏 Mac 환경 유니티 스팀 빌드 수동 업로드 가이드

이 가이드는 Windows용으로 제작된 유니티 스팀 빌더 플러그인이 Mac 환경에서 작동하지 않을 때, 스팀 공식 툴(SteamCmd)을 이용해 터미널로 직접 업로드하는 방법을 안내합니다.

---

## 📌 사전 준비 (최초 1회만 세팅)

### 1. VDF 설정 파일 세팅하기
`ContentBuilder/scripts` 폴더에 있는 두 개의 설정 파일 내용을 본인 게임에 맞게 수정합니다.

**1 `app_build_(메인 빌드 번호).vdf` (메인 설정 파일)**
~~~vdf
"AppBuild"
{
    "AppID" "(메인 빌드 번호)" 
    "Desc" "(빌드 설명)" 
    "Preview" "0" 
    "Local" "" 
    "SetLive" "" 
    "ContentRoot" "../content/" 
    "BuildOutput" "../output/" 
    "verbose" "0" 
    "Depots"
    {
        "(OS 빌드 번호)" "depot_build_(OS 빌드 번호).vdf"
    }
}
~~~

**2 `depot_build_(OS 빌드 번호).vdf` (상세 데포 설정 파일)**
~~~vdf
"DepotBuild"
{
    "DepotID" "(OS 빌드 번호)"

    "FileMapping"
    {
        "LocalPath" "*"
        "DepotPath" "."
        "Recursive" "1"
    }
    
    // Mac에서 자동 생성되는 숨김 파일 업로드 방지 (필수)
    "FileExclusion" "*.DS_Store"
}
~~~

### 2. 터미널 실행 권한 부여
Mac 터미널(Terminal)을 열고 스팀 실행 파일에 권한을 줍니다. (최초 1회만 실행하면 영구 유지됩니다.)
~~~bash
# 1. ContentBuilder 폴더로 이동 (본인 경로에 맞게 수정)
cd /Users/kark/Desktop/Private/05.Steam/sdk/tools/ContentBuilder

# 2. 실행 권한 2가지 부여
chmod +x builder_osx/steamcmd.sh
chmod +x builder_osx/steamcmd
~~~

---

## 🚀 게임 업데이트 루틴 (매번 반복)

새로운 버전을 스팀에 업로드할 때마다 아래 4단계를 순서대로 진행합니다.

### 1단계: 유니티 빌드 및 파일 복사
1. 유니티에서 일반적인 방법으로 게임을 빌드합니다.
2. 빌드되어 나온 파일들(exe, 데이터 폴더 등 전체)을 `ContentBuilder/content` 폴더 안에 **모두 덮어쓰기** 합니다.

### 2단계: 터미널 열고 폴더 이동
터미널을 새로 열었을 때는 반드시 `ContentBuilder` 폴더로 먼저 이동해야 합니다.
~~~bash
cd /Users/kark/Desktop/Private/05.Steam/sdk/tools/ContentBuilder
~~~
> 💡 **Mac 꿀팁:** 터미널에 `cd ` (띄어쓰기 포함)를 타이핑하고, 파인더에 있는 `ContentBuilder` 폴더를 터미널 창으로 드래그 앤 드롭한 뒤 엔터를 치면 편합니다.

### 3단계: 업로드 명령어 실행
경로 이동 후, 아래 명령어를 실행하여 스팀 서버에 업로드합니다.
~~~bash
./builder_osx/steamcmd.sh +login 본인아이디 본인비밀번호 +run_app_build ../scripts/app_build_(메인 빌드 번호).vdf +quit
~~~
> 💡 **Mac 꿀팁:** 터미널에서 **위쪽 화살표(↑)** 키를 누르면 이전에 쳤던 명령어를 불러올 수 있어 매번 타이핑할 필요가 없습니다.
> 🚨 **주의:** 새로운 기기나 오랜만에 접속 시 **Steam Guard 코드**를 터미널에 입력하라고 뜰 수 있습니다.

### 4단계: 스팀 파트너 사이트 적용
터미널 마지막에 `Success!`가 뜨면, 스팀웍스 대시보드의 **[SteamPipe -> 빌드]** 메뉴로 이동합니다. 방금 올라간 최신 빌드를 `default` 브랜치에 선택하고 **[라이브 적용]** 버튼을 누르면 유저들에게 업데이트가 배포됩니다.
