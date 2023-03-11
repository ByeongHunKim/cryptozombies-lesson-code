## 2 : contract

- 컨트랙트 컴파일러 버전 지정과 컨트랙트 선언

---

## 3 : state variables , integers

- uint256 === uint
- uint변수를 선언하고 값을 할당한다

---

## 4 : math operations

- uint변수에 10의 16승 값을 배정한다

---

## 5 : struct

- 구조체를 통해 여러 특성을 가진, 보다 복잡한 자료형을 생성할 수 있다

```solidity
struct Zombie {
	string name;
	uint dna;
}
```

---

## 6 : array

### 정적배열

- 고정된 크기가 있다

```solidity
uint[2] fixedArray;
```

### 동적배열

- 고정된 크기가 없다

```solidity
Person[] people;
```

## Public 배열

- 컨트랙트에 공개 데이터를 저장할 때 유용한 패턴이다

```solidity
Person[] public people;
```

<aside>
💡 Zombie 구조체의 public 배열을 생성하고 이름을 zombies로 한다

</aside>

```solidity
Zombie[] public zombies;
```

---

## 7 : function

<aside>
💡 createZombie라는 함수를 생성한다. 이 함수는 다음 2개의 인자를 전달받아야한다. _name(string), _dna(uint)

</aside>

```solidity
function createZombie(string _name, uint _dna) {}
```

---

## 8 : working with structs and arrays

<aside>
💡 함수에 코드를 넣어 새로운 Zombie를 생성하여 zombies 배열에 추가한다
새로운 좀비를 위한 name, dna는 createZombie함수의 인자값이어야한다.

코드를 한 줄로 간결하게 작성해보자

</aside>

```solidity
function createZombie(string _name, uint _dna) {
        zombies.push(Zombie(_name, _dna));
    }
```

---

## 9 : private / public function

<aside>
💡 우리 컨트랙트의 createZombies 함수는 기본적으로 public으로 선언되어있다
즉, 누구나 이 함수를 호출해서 새로운 좀비를 컨트랙트에서 만들 수 있다는 뜻이다. 이 함수를 private으로 선언해보자

</aside>

<aside>
💡 private 키워드가 있는 함수는 함수명도 언더바(_) 로 시작해야한다.

</aside>

```solidity
function _createZombie(string _name, uint _dna) private {
        zombies.push(Zombie(_name, _dna));
    }
```

---

## 10 : \***\*More on Functions\*\***

- 반환값
- 함수 제어자
  - view
    - 어떤 값을 변경하거나 무언가를 쓰지 않는다
    - 이는, 데이터를 보기만하고 변경하지 않는다는 의미이다
  - pure
    - 함수가 어떤 데이터도 접근하지 않는다
    - 다만, 반환값이 함수에 전달된 인자값에 따라서 달라진다

<aside>
💡 `_generateRandomDna`라는 `private` 함수를 선언. 이 함수는 `_str` (`string`형)을 인자로 전달받고, `uint`을 반환

이 함수는 컨트랙트 변수를 보지만 변경하지는 않을 것이므로 `view`로 선언

</aside>

```solidity
function _generateRandomDna(string str) private view returns(uint){

    }
```

---

## 11 : \***\*Keccak256 and Typecasting\*\***

<aside>
💡 코드 첫 줄에서는 `_str`을 이용한 `keccak256` 해시값을 받아서 의사 난수 16진수를 생성하고 이를 `uint`로 형 변환한 다음, `rand`라는 `uint`에 결과값을 저장해야 한다.

우리는 좀비의 DNA가 16자리 숫자이기만을 원하므로(`dnaModulus`를 기억하나?) 코드의 두번째 줄에서는 위의 결과 값을 모듈로(`%`) `dnaModulus`로 연산한 값을 반환해야 한다. (형식: `return` `위의 결과 값` `%` `dnaModulus`).

</aside>

```solidity
function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(_str));
        return rand % dnaModulus;
    }
```

---

## 12 : \***\*Putting It Together\*\***

<aside>
💡

1. `createRandomZombie`라는 `public`함수를 생성한다. 이 함수는 `_name`이라는 `string`형 인자를 하나 전달받는다. *(참고: 함수를 `private`로 선언한 것과 마찬가지로 함수를 `public`로 생성할 것)*
2. 이 함수의 첫 줄에서는 `_name`을 전달받은 `_generateRandomDna` 함수를 호출하고, 이 함수의 반환값을 `randDna`라는 `uint`형 변수에 저장해야 한다.
3. 두번째 줄에서는 `_createZombie` 함수를 호출하고 이 함수에 `_name`와 `randDna`를 전달해야 한다.
4. 함수의 내용을 닫는 `}`를 포함해서 코드가 4줄이어야 한다.
</aside>

```solidity
function createRandomZombie(string _name) public {
        uint randDna = _generateRandomDna(_name);
        _createZombie(_name, randDna);
    }
```

---

## 13 : event

<aside>
💡

좀비가 생성될 때마다 우리 앱의 사용자 단에서 이에 대해 알고, 이를 표시하도록 하는 이벤트가 있으면 좋겠네.

1. `NewZombie`라는 `event`를 선언한다. `zombieId` (`uint`형), `name` (`string`형), `dna` (`uint`형)을 인자로 전달받아야 한다.
2. `_createZombie` 함수를 변경하여 새로운 좀비가 `zombies` 배열에 추가된 후에 `NewZombie` 이벤트를 실행하도록 한다.
3. 이벤트를 위해 좀비의 `id`가 필요할 것이다. `array.push()`는 배열의 새로운 길이를 `uint`형으로 반환한다. 배열의 첫 원소가 0이라는 인덱스를 갖기 때문에, `array.push() - 1`은 막 추가된 좀비의 인덱스가 될 것이다. `zombies.push() - 1`의 결과값을 `uint`형인 `id`로 저장하고 이를 다음 줄에서 `NewZombie` 이벤트를 위해 활용한다.
</aside>

```solidity
pragma solidity ^0.4.19;

contract ZombieFactory {

    event NewZombie(uint zombieId, string name, uint dna);

    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;

    struct Zombie {
        string name;
        uint dna;
    }

    Zombie[] public zombies;

    function _createZombie(string _name, uint _dna) private {
        uint id = zombies.push(Zombie(_name, _dna)) -1 ;
        NewZombie(id, _name, _dna);

    }

    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(_str));
        return rand % dnaModulus;
    }

    function createRandomZombie(string _name) public {
        uint randDna = _generateRandomDna(_name);
        _createZombie(_name, randDna);
    }

}
```
