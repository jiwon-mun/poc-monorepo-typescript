# poc-monorepo-typescript

## 발견된 이슈

1. 모든 프로젝트간의 typescript 버전이 같아야 함.
   그렇지 않을 경우에 설정 공유가 이루어 지지 않음

2. Use VS Code's Version보다 높은 typescript 버전 사용 시에 SDK가 적용이 안되는 이슈가 있음. 때문에 4.8.2로 모든 프로젝트 고정 중임.

## 독립 가능하지 않은 이유

- typescript 버전

  - typescript 버전 자체는 독립적으로 구성하나 vscode sdk에 이슈가 있음. 기본적으로 vscode는 typescript 최신 버전을 항상 ts-server의 resolution으로 선택함. ( [문서 참고](https://code.visualstudio.com/docs/typescript/typescript-compiling#_using-newer-typescript-versions) )

  그렇지 않고 커스텀한 typescript 버전을 가져가려면

  ```
  "typescript.tsdk": ".yarn/sdks/typescript/lib",
  ```

  다음과 같은 세팅을 가져가야 함.

  이 때 해당 설정만으로는 dynamically resoultion을 가져가지 못하기 때문에

  tsserver.js라는 오버라이딩 파일을 reference하고 패키지마다 별도의 resoultion을 가져가게 하는 방식이 있긴 함.

  실제로 이를 활용한 [extension도](https://github.com/microsoft/vscode/issues/108866#issuecomment-753240892) 있으나 아직 검증된 것이 많이 없음.

  이에 대한 니즈는 [모노레포 진형과 pnp진영 모두 있으나](https://github.com/microsoft/vscode/issues/147660) 아직 starndard한 해결책은 제시되지 않은 상황임.

  때문에 가능하면 typescript 버전은 통일하는게 좋아 보임. 참고로 yarn berry내에서도 별도의 typescript 설치를 하되 버전은 통일 시킴.

  그 외 workspace에 typescript 설치하지 않고 root 사용하기등 시도해보았지만 @types등을 제대로 resolve 못하는 상황. ( 이유 모름 )

  다만 앞으로 새로운 프로젝트가 생성되게 되면 그 때마다 root와 typescript 버전이 달라질 수 있는데, 이 경우에 이를 감지할 수 있는 before installed hook은 필요할 수도 있음.
