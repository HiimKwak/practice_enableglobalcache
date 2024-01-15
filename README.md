# client_v2

모노레포로 새로워진 훕치치 클라이언트 레포지토리

1. pnp 모드
- enableGlobalCache 활성화(default): 의존성 zip파일들은 git에 올라가지 않아 좋으나 각 로컬머신 환경마다 .yarn/berry/cache 폴더의 위치가 제각각이어서(상대경로) .pnp.cjs의 경로가 매번 바뀌게 됨
- enableGloablCache 비활성화: 의존성 위치를 프로젝트 내부 .yarn/cache의 경로(절대경로)로 통일하기 때문에 상대경로 문제는 해결할 수 있지만 특정 의존성들의 크기가 매우 클 경우 git에 올리지 못하거나 git에 부하를 주게 됨(e.g. @next/swc-darwin-x64 = 110MB)
- 결론: Plug'n'Play 모드를 제대로 활용하려면 [enableGlobalCache](https://yarnpkg.com/configuration/yarnrc#enableGlobalCache) + [globalFolder](https://yarnpkg.com/configuration/yarnrc#globalFolder) 를 활성화하고, globalFolder를 각자의 로컬 경로가 아닌 하나의 원격 저장소 캐시 경로로 통일시켜줘야 의미가 있다.

2. node-modules 모드
- node-modules 모드는 여태껏 써왔던 npm과 yarn classic 그 자체다
- Editor SDK 설정을 따로 해줄 필요도 없고(`.vscode`, `.yarn/sdks` 불필요), root에 node_modules 폴더가 생성되며 각 workspaces 디렉토리에 추가적인 node_modules가 생성되는 것 말고는 특이사항이 없다
- pnp 모드가 패키지의 정보를 해시 테이블마냥 기록해줘 디스크 I/O 과정이 생략되고 패키지들이 zip 파일로 압축돼 디스크 공간을 절약할 수 있다 하지만, 실제로는 이상적인 장점들과는 거리가 있다
  - 최초의 프로젝트 설치 시 node_modules를 설치하는 과정이나 매우 큰 zip 아카이브를 다운로드하는 과정이나 비슷비슷하다(오히려 zip 아카이브를 다운로드하는데 걸리는 시간 + yarn install을 실행하는 시간이 합쳐지면 [npm보다 더 느려질 수 있다](https://helloinyong.tistory.com/344)).

3. pnpm 모드?