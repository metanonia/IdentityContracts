# LACChain Identity Contracts 분석 보고서

## 1. 프로젝트 개요
이 프로젝트는 **LACChain Identity Contracts**로, [lacchain-did-registry](https://github.com/lacchain/lacchain-did-registry)를 확장하여 DID(Decentralized Identifier) 레지스트리 기능을 제공합니다. 기본적으로 **ERC-1056** 표준을 따르며, 아이덴티티 관리, 위임(Delegation), 속성(Attribute) 관리 기능을 포함합니다. 또한, LACChain의 가스 모델(Gas Model)을 지원하기 위한 특별한 컨트랙트들도 포함되어 있습니다.

## 2. 주요 구성 요소 (Contracts)

`contracts/identity` 디렉토리 내에 핵심 로직이 위치하며, 크게 두 가지 구조로 나뉩니다.

1.  **Core Contracts (`didRegistry/`)**: DID 기능의 핵심 로직을 구현.
2.  **Gas Model Contracts (`didRegistryGasModel/`)**: Core 로직에 메타 트랜잭션(Meta-transaction) 지원을 추가.

### 2.1 Core Contracts

#### `DIDRegistry.sol`
DID 시스템의 기본이 되는 컨트랙트입니다.
*   **주요 기능**:
    *   **Controller 관리**: `controllers` 매핑을 통해 ID의 제어 권한을 관리합니다.
    *   **Delegate 관리**: `delegates` 매핑을 통해 특정 작업을 대행할 수 있는 대리인을 지정합니다.
    *   **Attribute 관리**: `attributes` 매핑을 통해 ID에 관련된 속성 데이터를 저장합니다 (double mapping 구조 사용).
    *   **Key Rotation**: `enableKeyRotation`을 통해 주기적인 키 교체를 설정할 수 있습니다.
    *   **서명 지원**: `changeControllerSigned`, `setAttributeSigned` 등의 함수를 통해 오프체인 서명을 이용한 상태 변경을 지원합니다.

#### `DIDRegistryRecoverable.sol`
`DIDRegistry`를 상속받아 **복구(Recovery)** 기능을 추가한 컨트랙트입니다.
*   **주요 기능**:
    *   **복구 메커니즘**: 제어 키를 분실했을 때, 다수의 Controller(최소 `minControllers`)의 동의를 통해 제어권을 복구할 수 있습니다.
    *   **보안 장치**: `maxAttempts`(최대 시도 횟수), `resetSeconds`(초기화 시간) 등을 두어 무작위 공격(Brute-force attack)을 방지합니다.
    *   **recover 함수**: 복구를 시도하는 핵심 함수로, 유효한 서명과 충분한 수의 Controller 동의가 확인되면 `recoveredKeys`에 추가하고, 과반수 이상이 모이면 Controller를 변경합니다.

### 2.2 Gas Model Contracts

#### `DIDRegistryGM.sol`
`DIDRegistry`와 `BaseRelayRecipient`를 다중 상속받은 컨트랙트입니다.
*   **주요 기능**:
    *   **메타 트랜잭션 지원**: 사용자가 가스비를 직접 지불하지 않고, 중계자(Relayer)를 통해 트랜잭션을 실행할 수 있도록 합니다.
    *   **_msgSender() 오버라이드**: 트랜잭션이 중계자를 통해 실행될 때, `msg.sender` 대신 원래 요청자의 주소를 반환하도록 로직을 변경했습니다 (`trustedForwarder` 사용).

#### `DIDRegistryRecoverableGM.sol` (추정)
`DIDRegistryRecoverable`에 Gas Model 기능을 더한 것으로, 복구 기능과 메타 트랜잭션 기능을 모두 제공할 것으로 보입니다.

## 3. 기타 사항
*   **Migrations.sol**: Truffle 등의 배포 도구에서 사용되는 마이그레이션 추적용 컨트랙트입니다.
*   **SafeMath.sol**: 오버플로우/언더플로우 방지를 위한 유틸리티 라이브러리입니다 (Solidity 0.8.x 이상에서는 내장되어 있지만, 호환성이나 명시적 사용을 위해 포함된 것으로 보입니다).

## 4. 요약
이 저장소는 **자기 주권 신원(SSI)** 구현을 위한 스마트 컨트랙트 모음입니다. 사용자는 기본적인 DID 기능만 필요하면 `DIDRegistry`, 키 복구 기능이 필요하면 `DIDRegistryRecoverable`을 사용할 수 있으며, 가스 대납(Gas Relaying) 환경에서는 `GM` (Gas Model) 접미사가 붙은 버전을 사용하여 배포하면 됩니다.
