# LACChain Identity Contracts

Did-Registry stack of contracts are an extension of [Lacchain did registry](https://github.com/lacchain/lacchain-did-registry/tree/master) which in turn inherits from [ERC-1056](https://github.com/ethereum/EIPs/issues/1056)

## Specifications

- [DID specifications](./DidSpecs.md)
- [DID Registry Specifications](./DidRegistry.md)
- [DID Resolver Specification](./DidResolverSpecs.md) | [Nodejs implementation](https://github.com/lacchain/lacchain-did-js)

## Getting Started

- [Configuration Guide](docs/tech/configuration.md)
- [Deployment Guide](docs/tech/deployment.md)
- [Testing Guide](docs/tech/testing.md)
- [Additional Scripts](docs/tech/additionalscripts.md)

- 일반적인 경우 (LACChain 사용 + 복구 기능 불필요): cripts/deployDidRegistry.ts를 실행하여 DIDRegistryGM 하나만 배포하면 됩니다.
- 복구 기능까지 필요한 경우: scripts/deployDidRegistryRecoverable.ts를 실행하여 DIDRegistryRecoverableGM 하나만 배포하면 됩니다.

## License

**Apache License, Version 2**
