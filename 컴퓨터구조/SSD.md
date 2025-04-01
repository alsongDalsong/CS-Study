# SSD의 구조

## 😇 시작하며
사실 너무 프로그래밍과 많은 관련이 없어... 이 시리즈는 SSD까지만 하려고 합니당. ㅠㅠ 뭔가 살짝(?) 의미없어보이기도 하고...

## 💾 ROM, RAM
SSD는 플래시 메모리로 만든 저장장치인데, ROM과 RAM이라는 키워드가 많이 보여 이것부터 간단하게 살펴봅시다.

<img width="300" src="https://github.com/user-attachments/assets/0386585f-7bbf-486c-9a75-9bc2f5a51940" />

메모리는 크게 휘발성, 비휘발성으로 나뉘는데 ROM은 비휘발성 메모리, RAM은 일반적으로 휘발성 메모리입니다. Read-Only Memory의 약자인 ROM은 전원을 꺼도 데이터가 지워지지 않기 때문에 컴퓨터의 바이오스(Basic Input Output System)나 펌웨어, 운영체제를 저장하는데 쓰였으나 최근에는 플래시 메모리로 대체되었다고 합니다. (위키피디아 출처)

<img width="600" src="https://github.com/user-attachments/assets/08ab275a-b82c-47ed-851c-b3bbaf2e36c5" />

설정을 세팅하고 그대로 출시하는 전기밥솥, 전기포트에는 Mask ROM을 사용하고 있다고 합니다. Random Access Memory의 약자인 RAM은 휘발성 메모리이며 임의의 영역에 접근하여 읽고 쓰기가 가능한 주기억 장치입니다. ROM은 고정된 데이터로서 읽기만 가능하지만 RAM은 읽기와 쓰기가 가능하다는 큰 차이점이 있습니다.

여기서 RAM이 일반적으로 비휘발성 메모리라고 했는데, 전원이 꺼져도 지워지지 않는 PRAM, MRAM 처럼  NVRAM(Non-Volatile RAM)같은 RAM이 있기 때문입니다. 하지만 전통적으로 구분 할 때는 휘발성, 비휘발성으로 크게 나누는 것 같습니다.

플래시 메모리의 경우 EEPROM(Electrically Erasable Programmable Read-Only Memory)에서 발전되었다. EEPROM은 전기적으로 데이터를 지울 수 있고, 새로운 데이터를 프로그래밍하여 저장할 수 있는 읽기 전용 메모리입니다.

<img width="600" src="https://github.com/user-attachments/assets/e0f7078b-d37b-4505-ac23-52315da5ed20" />

여기서 플래시 메모리는 사실 ROM에서 발전되었지만 데이터를 페이지 단위로 접근하고 읽고 쓰기가 가능하기 때문에... 위키피디아 한글에서는 플래시메모리가 ROM, RAM의 종류에 둘다 들어가 있지만 아예 새로운 카테고리로 생각해야 할 것 같습니다? (슬슬 위키피디아도 뭔가... 한글은 정보가 빈약하다는 느낌이 드는 것 같습니다...)

<img width="500" src="https://github.com/user-attachments/assets/86ae7f6a-604a-456b-8f7f-fe9778a4e76c" />

## 📸 Flash Memory

### NOR, NAND

플래시메모리는 빠른속도로 읽고 쓴다고 해서 Flash라는 이름이 붙었다고 합니다. 대표적으로는 NOR, NAND 플래시가 있습니다.

NOR의 경우 셀이 병렬연결, NAND의 경우 셀이 직렬연결 되어있습니다. NOR의 경우 병렬연결이기 때문에 셀 단위로 접근이 가능해서 읽기가 빠르지만 셀 하나씩 기록해야하기 때문에 쓰기가 느리고, NAND의 경우 직렬로 연결되어 있어서 연결된 셀이 한꺼번에 동작해서 읽기가 느리지만 한 블록을 통째로 기록하기 때문에 쓰기가 빠릅니다.

하나하나 연결해주는 병렬 연결보다 직렬 연결이 배선수를 줄일 수 있어 더 고밀도화가 가능하다고 합니다.

<img width="600" src="https://github.com/user-attachments/assets/d088902a-236a-425d-b415-7f3c2e870b9c" />

### Floating gate

NOR 플래시, NAND 플래시 둘다 플로팅 게이트 MOSFET을 사용하게 됩니다. 플로팅 게이트 트랜지스터는 아래와 같이 생겼는데 이러한 플로팅 게이트가 모여있는 것이 플래시 메모리 입니다. 이러한 플래시 메모리, NAND 플래시 메모리 방식으로 만든 것이 SSD라고 합니다.

<img width="600" src="https://github.com/user-attachments/assets/ea0f7244-6845-42f7-a0c5-3a0cec3f103a">

이전에는 전하가 있거나 없거나로 1비트의 개념(SLC, Single Level Cell)이었다면 현재에는 플로팅 게이트에 전하 수준을 세밀하게 제어하여 더 많은 수를 표현할 수 있는데 이를 (MLC, Multi Level Cell)고 합니다...!

## 🫥 SSD

드디어 SSD네요... [영상](https://youtu.be/E7Up7VuFd8A) 참고했습니다.

기존의 트랜지스터를 간단히 설명하면 위와 같이 전압이 걸리면 전자가 흐릅니다.

<img width="600" src="https://github.com/user-attachments/assets/2546f1ad-b934-4538-a4fa-d4af1f58da26" />

아래와 같이 전압을 걸어주게 되면 charge trap에 전자를 아래처럼 가져오게 되고 그 전자는 갖히게 됩니다.

<img width="600" src="https://github.com/user-attachments/assets/7401e7bf-467c-4133-b186-140b50db6900" />


전하가 갖혀있게 된다면 아래와 같이 전자를 흐르게 하기 위해서 더 큰 전압이 필요한데 이 차이가 데이터가 된다고 생각하면 될 것 같습니다.

<img width="600" src="https://github.com/user-attachments/assets/65819185-4649-442e-ad7a-cffb72b421d3" />

위에서 알아봤듯이 NAND 플래시 메모리는 이렇게 셀들이 직렬로 연결이 되어있습니다.

<img width="600" src="https://github.com/user-attachments/assets/e7014035-b1ac-4d11-9a7e-e06d819b2300" />

따라서 전압을 걸어주면서 직렬로 연결된 셀의 데이터 현황을 파악하는 것으로 데이터를 읽게 됩니다.

<img width="600" src="https://github.com/user-attachments/assets/c5aa530a-9088-4b4b-b6c0-87e7ccbf35b4" />
<img width="600" src="https://github.com/user-attachments/assets/9d0a2c11-22c6-4467-8b0d-20ecd6cdcf5b" />

이것들이 모이고, 모이고, 모여서 SSD가 된다고 생각하면 될 것 같습니다. 이 셀들을 균등하게 사용해서 셀의 수명을 연장시키는 웨어 레벨링(Wear Leveling)이나 오래된 데이터를 정리하는 가비지 컬렉션(Garbage Collection) 등 성능 최적화 기법을 사용하고 있다고 합니다. SSD는 HDD와 같이 기계적 지연이 없어 데이터 배치를 효율적으로 하기 위한 알고리즘이 사용된다고 합니다.

SSD의 수명은 쓰기 횟수에 따라 결정되지만, 제조사에서는 용량(TBW, Total Bytes Written)을 기준으로 수명을 표시한다고 한다. 또한 SSD를 사용하지 않고 몇년동안 보관하게 된다면 시간이 지나면 전자가 빠져나가 데이터 손상의 위험이 있다고 합니다.
