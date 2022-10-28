---
title: "Under the hood of Node.js require"
---

## 개요

Node.js 애플리케이션을 개발하면서 모듈을 가져올 때 `require()` 함수를 많이 써왔는데, 실제로 Node.js가 내부적으로 어떻게 모듈을 가져오는지는 알 수 없었다.

Node.js 런타임 분석 툴을 개발하며 이러한 내부 동작 방법을 알아야 했고, 따라서 Node core 라이브러리를 파헤쳐보기로 하였다. Node.js core의 중심에는 `module.js`라는 파일이 있고, 여기서 `require()` 메커니즘에 대한 해답을 찾을 수 있다.

## module.js

`module.js`에 있는 `Module` 타입은 Node.js에서 두 가지 주요 역할을 갖는다.

첫째, 모든 Node.js 모듈들을 만들기 위한 토대를 제공해준다. 각 파일은 로딩될 때, 이 베이스 모듈의 인스턴스를 받는데, 이는 파일이 실행된 후에도 남아있다(persists). 이러한 이유로 우리는 `module.exports`에 프로퍼티를 붙여 나중에 쓸 수 있는 것이다.

둘째, Node의 모듈 로딩 메커니즘을 담당한다. 우리가 주로 사용하는 standalone `require` 함수는 사실 `module.require`의 추상화(abstraction)이고, 또, `module.require`는 `Module._load`를 감싸는 간단한 wrapper이다. 이 load 메소드에서 실제로 각 파일의 로딩이 처리된다.

## Module._load

```javascript
Module._load = function (request, parent, isMain) {
    // 1. Check Module._cache for the cached module.
    // 2. Create a new Module instance if cache is empty.
    // 3. Save it to the cache.
    // 4. Call module.load() with the given filename
    //    This will call module.compile() after reading the file contents.
    // 5. If there was an error loading/parsing the file,
    //    delete the bad module from the cache.
    // 6. return module.exports
};
```

`Module._load`는 새로운 모듈을 로딩하고 모듈 캐시를 관리한다. 로딩할 때 각 모듈을 캐싱하여 중복된 파일 읽기 횟수를 줄일 수 있어서 애플리케이션의 속도를 증가시킬 수 있다. 또한, 모듈 인스턴스들을 공유함으로써 각 모듈은 프로젝트 범위로 자신의 상태를 유지하는 싱글톤 느낌의 객체가 될 수 있다.

캐시에 모듈이 존재하지 않는다면, `Module._load`는 해당 파일에 대한 새로운 베이스 모듈을 만들게 된다. 그리고 나서 모듈에게 새로운 파일의 내용을 읽은 후, `module._compile`에게 전달한다.

위에서 6번 단계를 보면, `module.exports`가 사용자에게 리턴되는 것을 알 수 있다. 이것이 우리가 퍼블릭 인터페이스를 정의할 때 `exports`와 `module.exports`를 사용하는 이유다 - `Module._load`가 이를 리턴하고, 따라서 그 후 `require`도 이를 리턴하게 되는 것이다.

## Module._compile

```javascript
Module.prototype._compile = function (content, filename) {
    // 1. Create the standalone require function that calls module.require.
    // 2. Attach other helper methods to require.
    // 3. Wraps the JS code in a function that provides our require,
    //    module, etc. variables locally to the module scope.
    // 4. Run that function.
};
```

이 메소드에서 우리가 알고 있는 standalone `require` 함수를 `Module.require`를 래핑하는 함수로 정의하고, `require`에 여러가지 helper 프로퍼티와 메소드를 넣어준다. 그 후, 로딩되는 전체 소스 코드를 새로운 함수로 래핑해주는데, 이 함수는 `require`, `module`, 그리고 `exports` 등의 노출되는 변수들을 매개변수로 갖는다. 이를 통해 해당 모듈만을 위한 새로운 함수형 범위 (functional scope)를 만들 수 있어서 나머지 Node.js 환경에 영향을 주지 않게된다.

```javascript
(function (exports, require, module, __filename, __dirname) {
    ...
});
```

최종적으로, 모듈을 래핑한 이 함수가 실행된다. `Module._compile` 메소드는 동기로 실행되므로, 이를 호출한 `Module._load` 메소드는 `Module._compile` 메소드 종료를 기다렸다가 `module.exports`를 사용자에게 리턴한다.

## 결론

`require` 메소드를 호출하는 것으로 시작해서, 내부적으로 어떻게 모듈이 로딩되고 컴파일되는지 알아보았다. 심지어 `require('module')`을 통해 모듈 시스템을 모듈 시스템으로 로딩할 수 있다. 이게 가능함으로써 우리는 Node.js core 코드를 파고들지 않고 로딩 시스템과 상호작용할 수 있다.

## 출처

<http://fredkschott.com/post/2014/06/require-and-the-module-system/>
