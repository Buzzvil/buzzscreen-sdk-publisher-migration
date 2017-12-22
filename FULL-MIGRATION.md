## Full Migration SDK Integration
- M앱의 버즈스크린을 L앱의 버즈스크린으로 완전히 마이그레이션하고, 마이그레이션 이후에는 M앱에 대한 의존성이 없습니다.
- **L앱 마이그레이션 주의** : 이 방식을 사용하기 위해서 L앱은 미리 독립적인 어플리케이션으로 동작하기위한 기반 작업(ex. 로그인 관리)뿐만 아니라 M에서 기존에 연동한 버즈스크린을 동일한 형태로 L앱에도 연동이 되어있어야 합니다.
    > 마이그레이션 SDK는 M앱에서 L앱으로의 자연스러운(최소한의 유저 액션) 잠금화면 전환 작업만을 포함
- 마이그레이션 가이드의 모든 작업은 `sample_main` 및 `sample_lock` 에서 확인할 수 있습니다.
    > 샘플앱의 동작을 위해서는 샘플앱내의 `my_app_key`(`build.gradle`, `BuzzScreen.init` 확인), `AndroidManifest.xml`의 `<app_license>`와 `<plist>`를 발급받은 값으로 교체해주세요. 
- M앱과 L앱 각각 마이그레이션을 위한 추가 연동작업은 다음 링크들을 통해 확인할 수 있습니다. 

#### [M앱 마이그레이션 구현](FULL-MIGRATION-M.md)
#### [L앱 마이그레이션 구현](FULL-MIGRATION-L.md)
