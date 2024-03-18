# 🖥️ Escape Protocol

+ 몬스터를 피해 열쇠를 찾고 탈출하세요!
+ 여러가지 무기를 찾아서 적에게 도망가세요!
+ W/A/S/D 이동, 마우스 좌클릭으로 총 쏘기, 마우스 우클릭으로 정조준, 쉬프트로 달리기
<br/>

## 📽️ 프로젝트 소개
 - 게임 이름 : Escape Protocol
 - 플랫폼 : PC
 - 장르 : 3D 공포 탈출 FPS 
 - 개발 기간 : 24.01.02 ~ 24.01.08
<br/>

## ⚙️ Environment

- `Unity 2022.3.2`
- **IDE** : Visual Studio 2019, 2022, MonoDevelop
- **VCS** : Git (GitHub Desktop)
- **Envrionment** : PC `only`
- **Resolution** :	1920 x 1080 `FHD`
<br/>

## 👤 Collaborator - Team Intro
- 팀장  `최성재` - 몬스터 구현
- 팀원1 `고현규` - 맵 구현
- 팀원2 `성연호` - UI 구현
- 팀원3 `변정민` - Player 구현
- 팀원4 `나재민` - 총기 구현
<br/>

## ▶️ 게임 스크린샷

<p align="center">
  <img src="https://github.com/GAMGDOG/UnityTeamProject_2024_01_02/assets/149379194/7828c86e-4dde-4601-be6e-f03253fa70a8" width="49%"/>
  <img src="https://github.com/GAMGDOG/UnityTeamProject_2024_01_02/assets/149379194/463404d1-37e0-48d3-9623-210877bb73a9" width="49%"/>
</p>
<p align="center">
  <img src="https://github.com/GAMGDOG/UnityTeamProject_2024_01_02/assets/149379194/10c35733-6d52-4d46-83c4-fea6360ce3be" width="49%"/>
  <img src="https://github.com/GAMGDOG/UnityTeamProject_2024_01_02/assets/149379194/ef5d987d-5f20-4f06-aaa9-341dac8bbe6e" width="49%"/>
  </p>
<p align="center">
  <img src="https://github.com/GAMGDOG/UnityTeamProject_2024_01_02/assets/149379194/6766bd34-83e9-49be-aee5-d9ab0d3059a4" width="49%"/>
  <img src="https://github.com/GAMGDOG/UnityTeamProject_2024_01_02/assets/149379194/cd34afb4-389b-442e-8bae-163c19f7f46d" width="49%"/>
</p>
<p align="center">
  <img src="https://github.com/GAMGDOG/UnityTeamProject_2024_01_02/assets/149379194/5ffe6eb3-a1a2-4c93-b861-a360029c0259" width="49%"/>
  <img src="https://github.com/GAMGDOG/UnityTeamProject_2024_01_02/assets/149379194/0a970e77-75db-4c28-a0c0-8d252a839e38" width="49%"/>
</p>
<br/>

## ✏️ 구현 기능

### 1. 총기 구현
<img src="https://github.com/JaeMinNa/Ocean_Bloom/assets/149379194/c9e5fc85-379d-4666-a2f8-c4e55928fdaa" width="50%"/>

- Physics.Raycast로 총알 프리팹 생성 없이 총기 구현
```C#
if (Physics.Raycast(Camera.main.transform.position, Camera.main.transform.forward, out _hitInfo, 50f))
{
    Debug.Log(_hitInfo.transform.name);
}
```

- 카메라 rotation 값을 변경해서 총기 반동을 구현
```C#
IEnumerator CORetroAction()
{
    Vector3 recoilBack = new Vector3(CurrentGun.OriginPos.x, CurrentGun.OriginPos.y, CurrentGun.OriginPos.z - CurrentGun.RetroActionForce);
    CurrentGun.transform.localPosition = CurrentGun.OriginPos;

    // 반동 시작
    while (CurrentGun.transform.localPosition.z >= CurrentGun.OriginPos.z - CurrentGun.RetroActionForce + 0.02f)
    {
        CurrentGun.transform.localPosition = Vector3.Lerp(CurrentGun.transform.localPosition, recoilBack, 0.4f);
        _pov.m_VerticalAxis.Value += -CurrentGun.RetroActionForce;
        yield return null;
    }

    // 원위치
    while (CurrentGun.transform.localPosition != CurrentGun.OriginPos)
    {
        CurrentGun.transform.localPosition = Vector3.Lerp(CurrentGun.transform.localPosition, CurrentGun.OriginPos, 0.1f);
        yield return null;
    }
}
```

- Raycast의 시작점에서 일정한 랜덤 값을 더해서 총기 정확도 구현
```C#
private void Hit()
{
    Vector3 randomRange = new Vector3(Random.Range(-_currentGun.Accuracy, _currentGun.Accuracy), Random.Range(-_currentGun.Accuracy, _currentGun.Accuracy), 0);

    if (Physics.Raycast(Camera.main.transform.position, Camera.main.transform.forward + randomRange, out _hitInfo, _currentGun.Range))
    {
        GameObject clone = Instantiate(_currentGun.HitEffectPrefab, _hitInfo.point, Quaternion.LookRotation(_hitInfo.normal));
    }
}
```
<br/>

### 2. 총기 교체 구현
<img src="https://github.com/JaeMinNa/Ocean_Bloom/assets/149379194/bbb79a30-6e92-4124-bd3c-720e275679a1" width="50%"/>

- 총 아이템을 먹으면 무기가 교체되도록 구현
```C#
public void EquipM4()
{
    foreach(GameObject gun in _gunHolders)
    {
        gun.SetActive(false);
    }

    CurrentGun = _gunHolders[1].GetComponent<Gun>();
    _gunHolders[1].SetActive(true);
}
```

- Player의 자식에 있는 GunHolder에서 해당 무기가 SetActive(true) 설정, 기존 무기는 SetActive(false)
<br/>

## 💥 트러블 슈팅

### 1. VirtualCamera의 총기 반동 구현
- MainCamera의 rotation 값을 변경해도 카메라 회전이 적용되지 않음
 <img src="https://github.com/JaeMinNa/Ocean_Bloom/assets/149379194/1f8fd08a-bed3-42cd-92e5-32bc33194c86" width="50%"/>

```C#
while (_currentGun.transform.localPosition.z >= _originPos.z - _currentGun.RetroActionForce + 0.02f)
{
    _currentGun.transform.localPosition = Vector3.Lerp(_currentGun.transform.localPosition, recoilBack, 0.4f);
    Camera.main.transform.localEulerAngles += new Vector3(-10, 0, 0);
    yield return null;
}
```
 
- Player가 시네머신 카메라를 사용하기 때문에 VirtualCamera에 접근해서 CinemachinePOV의 VerticalAxis 값을 변경
<img src="https://github.com/JaeMinNa/Ocean_Bloom/assets/149379194/d7b8cf0c-625d-4e5a-926c-baaccd155e6b" width="50%"/>

```C#
using Cinemachine;

[SerializeField] private CinemachineVirtualCamera _virtualCamera;
private CinemachinePOV _pov;

private void Awake()
{
    _pov = _virtualCamera.GetCinemachineComponent<CinemachinePOV>();
}

IEnumerator CORetroAction()
{
    while (_currentGun.transform.localPosition.z >= _originPos.z - _currentGun.RetroActionForce + 0.02f)
    {
        _currentGun.transform.localPosition = Vector3.Lerp(_currentGun.transform.localPosition, recoilBack, 0.4f);
        _pov.m_VerticalAxis.Value += -_currentGun.RetroActionForce;
        yield return null;
    }
}
```
<br/>

### 2. Physics.Raycast의 방향을 조절한 총기 정확도 구현
- 기존 Raycast의 시작점에서 일정한 랜덤 값을 더해서 구현한 방법은 멀리 있는 물체를 쏠 때보다 가까이 있는 물체를 쏠 때, 정확도가 더 낮아짐
<img src="https://github.com/JaeMinNa/Ocean_Bloom/assets/149379194/c86b3603-12c6-43a4-a631-2636a58f62bc" width="50%"/>

```C#
private void Hit()
{
    Vector3 randomRange = new Vector3(Random.Range(-_currentGun.Accuracy, _currentGun.Accuracy), Random.Range(-_currentGun.Accuracy, _currentGun.Accuracy), Random.Range(-_currentGun.Accuracy, _currentGun.Accuracy));

    if (Physics.Raycast(Camera.main.transform.position + randomRange, Camera.main.transform.forward, out _hitInfo, _currentGun.Range))
    {
        GameObject clone = Instantiate(_currentGun.HitEffectPrefab, _hitInfo.point, Quaternion.LookRotation(_hitInfo.normal));
    }
}
```

- 거리가 가까울수록 MainCamera의 중앙에서 벗어나는 실제 거리가 멀어짐
- Raycast의 방향을 바꾸는 방법으로 멀어질수록 총기 정확도가 낮아지도록 개선
<img src="https://github.com/JaeMinNa/Ocean_Bloom/assets/149379194/bd449d77-a326-47b6-89b8-4babd59688fd" width="50%"/>

```C#
private void Hit()
{
    Vector3 randomRange = new Vector3(Random.Range(-_currentGun.Accuracy, _currentGun.Accuracy), Random.Range(-_currentGun.Accuracy, _currentGun.Accuracy), 0);

    if (Physics.Raycast(Camera.main.transform.position, Camera.main.transform.forward + randomRange, out _hitInfo, _currentGun.Range))
    {
        GameObject clone = Instantiate(_currentGun.HitEffectPrefab, _hitInfo.point, Quaternion.LookRotation(_hitInfo.normal));
    }
}
```
<br/>

## 🎮 전체 구현 기능
* 캐릭터의 이동 및 기본 동작
  * 캐릭터, 몬스터 FSM 구현
  * W/A/S/D 이동 구현
* 레벨 디자인
  * Stage 1 : Key 1개를 찾아서 탈출
  * Stage 2 : Key 3개를 찾아서 탈출
* 충돌 처리 및 피해량 계산
  * RayCast로 Player 공격 구현
* UI/UX 요소
  * Player Hp, Stamin 구현
  * 총알 갯수 표시
  * Loby, 일시정지 구현
* 다양한 무기
  * HandGun1, M4, M82 구현
* 난이도 설정
  * Normal, HardCore 설정
  * HardCore 시, 몬스터 이동속도 증가
* 미션 시스템
  * 현재 수행해야 할 미션 표시
* AI 적 캐릭터
  * 일정 범위 안의 Player를 쫒아오는 몬스터 구현
* 사운드 및 음악 효과
  * 배경음 구현
  * 여러가지 기타 사운드 구현
* 특수 효과 및 파티클 시스템
  * 총기 Effect, Particle System 구현
* 총기 구현
  * 마우스 좌 클릭으로 총 발사
  * 총기 반동, 정확도 구현
<br/>

## 📋 프로젝트 회고
### 잘한 점
 - 실제 총기와 비슷하게 총기 구현
 - 유니티 3D 프로젝트를 처음으로 시도
<br/>

### 한계
- 2개의 스테이지만 존재
- 적, 아이템 등 게임 요소가 부족
<br/>

### 소감
처음 유니티 3D에 도전한 프로젝트였고, 전체적으로 좋은 평을 받을 수 있어서 의미가 있는 프로젝트였습니다. 직접 고민하고 구현한 총기에 대해서 좋은 평을 받아서 보람을 크게 느꼈습니다.
