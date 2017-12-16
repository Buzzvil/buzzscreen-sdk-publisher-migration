# BuzzScreen Migration Guide
- 기존 [버즈스크린을 연동](https://github.com/Buzzvil/buzzscreen-sdk-publisher)한 어플리케이션을 새로운 어플리케이션으로 마이그레이션을 하기 위한 가이드
    > 이하 기존 버즈스크린을 연동한 어플리케이션을 M(Main) 앱으로 지칭하고, 새로운 어플리케이션을 L(LockScreen) 앱으로 지칭
- 이 가이드를 통해 L앱의 설치과정을 제외한 모든 과정을 자동화하여 M앱의 버즈스크린 사용자를 L앱의 버즈스크린 사용자로 자연스럽게 전환 가능
    > L앱의 설치(APK 파일 다운로드 & 설치)는 유저의 액션없이 진행할 수 없기에 자동화가 불가능. 자동화의 범위 및 과정은 추후 기술
- **L앱 마이그레이션 주의** : L앱은 마이그레이션 가이드 작업전에 미리 버즈스크린 연동 작업이 M앱과 동일한 형태로 되어있어야 합니다. 
    > 마이그레이션 가이드는 M앱에서 L앱으로의 자연스러운(최소한의 유저 액션) 잠금화면 전환 작업만을 포함합니다.
- **마이그레이션 진행시 주의** : 마이그레이션을 진행하기 위해서는 **반드시 미리 버즈빌 BD 팀과 협의 후 진행** 을 해야하며, 가이드 모두 반영한 M앱과 L앱의 APK 파일들은 마켓에 업로드하기전에 버즈빌 BD 팀에 전달하여 **반드시 리뷰 후 마켓에 업로드**해야 합니다.
- 마이그레이션 가이드의 모든 작업은 `sample_main` 및 `sample_lock` 에서 확인할 수 있습니다.
- M앱과 L앱 둘다 마이그레이션을 위한 추가 연동작업이 있으며 다음 링크들을 통해 확인할 수 있습니다. 

### [M앱 마이그레이션 연동](MIGRATION-M.md)
### [L앱 마이그레이션 연동](MIGRATION-L.md)

