# Windows
## Visual Studio

### 특정 프로세스를 디버거에 붙이기
두 개의 서로다른 어플(a.exe 와 b.exe)을 하나의 솔루션안에서 개발하고 있다고 합시다.
이때 어플 a.exe 가 실행중에 다른 어플 b.exe 를 실행시킨다고 하면, 보통 b.exe 의 소스에 해당하는 곳에 브레이크 포인트를 잡기가 쉽지 않습니다. 

특히 b.exe 가 자신이 할 일을 매우 빨리 끝내고 close 되어 버리면 미처 Visual Studio 의 attach 기능으로 b.exe 를 디버거에 붙이기도 전에 b.exe 는 이미 끝나버리니까요.
그래서 여러가지 편법이 동원됩니다. 예를 들면 b.exe 가 시작되면 우선 Visual Studio 의 attach 기능으로 b.exe 를 디버거에 붙일수 있을 정도의 시간만큼 sleep 시키기도 합니다.

그런데 지금 소개시켜 드리는 방법으로는 b.exe 가 실행되자마자 윈도우에서 이 프로세스를 디버깅 할것인지 우선 물어보는 창을 띄워줍니다. 그럼 여유있게 이 b.exe 프로세스를 디버거에 붙인다음 이 창을 닫아주면 b.exe 프로세스가 계속 진행되기 시작해서 얼마든지 b.exe 에 해당하는 소스코드에 브레이크 포인터를 걸 수 있습니다.


1. regedit.exe 을 실행합니다.
2. HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options 로 가세요.
3. b.exe 를 위한 새로운 키를 만듭니다.
4. 새로운 string 값을 b.exe 아래에 만들고 string 이름은 'Debugger' 로 하고 그 값은 'vsjitdebugger.exe' 로 해줍니다.

아래는 위의 4 단계를 한번에 해주는 레지스트리 스크립트의 예제입니다.

> Windows Registry Editor Version 5.00
>
> [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\b.exe]
> "Debugger"="vsjitdebugger.exe"

이렇게 해주면 b.exe 가 실행될때마다 창이 하나 뜨면서 이 프로세스를 위해 새로운 디버거를 실행시키고 연결할지를 묻게 되는데, 
이 창을 그대로 둔 채로 visual studio 에서 b.exe 를 작업중이던 디버거에 attach 시킨뒤에 원하는 곳에 브레이크 포인트를 잡고 
새롭게 뜬 창은 그냥 닫아주면 됩니다.

출처: https://blogs.msdn.microsoft.com/greggm/2005/02/21/inside-image-file-execution-options-debugging/
