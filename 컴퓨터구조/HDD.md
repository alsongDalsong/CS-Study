# HDD (Hard Disk Drive)

<img width="600" src="https://github.com/user-attachments/assets/625d5ab6-0667-492c-923d-0c23e17020dc" />

컴퓨터 시스템을 보면 위와 같습니다. 결국 컴퓨터는 CPU, Memory, I/O 장치들로 이루어져 있는 것을 볼 수 있습니다. 이 그림을 참고해 본다면 disk도 결국 I/O 장치에 가깝다고도 생각이 듭니다.

<img width="600" src="https://github.com/user-attachments/assets/7524fc3c-76e2-4f7e-a56a-4d8571debd39" />

Primary Storage, Secondary Storage... 주기억 장치 보조기억 장치로 구분되는 HDD, SSD, DRAM에 대해서 알아보려고 합니다. 그중에 우선 HDD부터 알아보면 좋을 것 같습니다. 모두가 알고 있듯이 HDD는 hard-disk drives에 속한다고 볼 수 있고, SSD는 nonvolatile memory에 속하겠죠? DRAM은 main memory에 속할 것이구요. 속도와 가격은 HDD < SSD < DRAM 으로 볼 수 있겠습니다. (여기서 가격은 메모리당 가격)

## HDD의 구조

우선 HDD의 구조부터 살펴볼 예정입니다. 처음 HDD를 접했을때 생각보다 아날로그...? 라는 생각이 먼저 들었던 기억이 납니다. 이제 저 내부를 찬찬히 살펴볼 것입니다. 혹시나 하는데 집에 있는 하드를 절대 뜯어볼 생각을 하면 안됩니다. 그 이후로 못쓰게 될 확률이 높습니다. 왜인지는 이 발표가 끝나면 정확하게 이야기 할 수 있을 것이라고 생각됩니다. 😇

<img width="600" src="https://github.com/user-attachments/assets/01155785-73c6-47f6-8da5-f08fb05d6000" />

디테일한 구조는 아래와 같습니다. 뭐 여기까진 복습하는 느낌으로 봐주시면 좋을 것 같습니다. Disk 혹은 Platter 라고 불리는 원판, Platter를 읽는 Header, Header를 움직이는 Arm으로 이루어져 있습니다. Platter는 Track, Sector로 이루어져 있습니다.

<img width="600" src="https://github.com/user-attachments/assets/bed41c98-0394-4229-a658-ccfd706a4c87" />

## HDD의 구조

그래서 구조는 알겠는데 어떻게 읽고 쓰는데? 유튜브 [How do Hard Disk Drives Work](https://youtu.be/wtdnatmVdIg) 에 자세히 나와있습니다! 한번 같이 살펴볼까요? 참고로 이번에 한번보고 다시는 안보겠다는 마음가짐으로 보았습니다. iOS 개발자의 입장에서 전자공학적인 구조 자체가 크게 중요한건 아니니까요(?)

일단 Disk, Platter 중에 Platter 라고 용어를 통일하겠습니다. Platter 는 쉽게 알루미늄 합금으로 이루어져 있다고 생각하면 됩니다. 저 위에 작은 한줄 보이시나요?

<img width="600" src="https://github.com/user-attachments/assets/9861349f-e35b-47e9-a4bb-be85c0550198" />

그걸 확대한다면 아래와 같습니다. 우리가 생각했던 느낌의 그 레이어 등장! 이것도 코발트 크롬 합금으로 이루어져 있고, 아무튼 엄청 얇게 되어있습니다. 여기는 그냥 이렇구나 정도로 넘어가면 될 것 같습니다. 이제 저기에 보이는 N극 S극의 위치에 따라서 데이터가 저장될 것 같네요?

<img width="600" src="https://github.com/user-attachments/assets/a945b776-4966-45d2-91f6-bcf92465ab80" />

Arm을 한번 살펴봅시다.이 Arm이 Platter 위를 왔다갔다 하면서 트랙을 오고가며 정보를 읽고 쓰게 됩니다. 한면에는 약 50만개 + 트랙이 있다고 하네요. 여기서 파란원 하나가 트랙입니다.

<img width="600" src="https://github.com/user-attachments/assets/9573217c-3cc6-43a6-a5d5-8f2ba51b4af9" />

이런 Arm이 Platter를 15nm 위에서 움직이게 되는데 Platter는 영상에서는 7200RPM으로 움직인다고 합니다. 초당 120번 회전하네요. 참고로 머리카락은 약 80000nm입니다. 위에서 말했던 것인데 만약에 먼지가 들어오게 된다면 15nm위에서 움직이는 Arm과 Platter사이에 끼기라도 한다면... 끔찍하죠?

<img width="600" src="https://github.com/user-attachments/assets/963dbea6-9a80-47bc-a64f-6f6d194fc03f" />

이렇게 읽은 데이터는 PCB(전자 회로 기판)으로 신호를 보내겠죠?

<img width="600" src="https://github.com/user-attachments/assets/6a64b37e-d5b2-43a5-b052-4e2510b23fda" />

PCB에는 크게 Processor, DRAM chip, Controller chip이 위치하고 있는데, 여기도 DRAM이 있네요? 만약에 면접에서 DRAM에 대해서 물어본다면 어디있는 DRAM 말씀하시는건가요? 라고 물어볼 수 있을 것 같습니다. (절대 안될듯)

<img width="600" src="https://github.com/user-attachments/assets/b22b7cfd-3e7d-4ac7-aac3-7dbcaa0f36ad" />

## HDD 작동 원리

구조는 잘 살펴봤고 실제로 **어떻게** 읽고 쓰는지 알아볼까요?

디스크는 아래와 같이 Track 과 Track 을 잘게 나눈 Sector 로 이루어져 있습니다. 혹~시 헤깔리시다면 경기장의 Track 한바퀴 돌죠? 이거 생각하시면 좋을 것 같습니다.

<img width="600" src="https://github.com/user-attachments/assets/e77d6191-3a86-4186-9ae1-6a78a49a7591" />

각 Sector 는 데이터만 있는건 아닙니다. 마치 프로토콜마냥 야무지게 구성되어 있습니다. Header, Data 로 이루어져 있고 그 뒤에 약간의 Gap을 둔다 정도만 알면 될 것 같습니다. 2년전 영상에서 Sector 한조각에 약 4KB의 데이터가 저장되어 있다고 했으니 지금은 더 많아졌을 수도 있겠어요.

<img width="600" src="https://github.com/user-attachments/assets/20bcf557-135b-48d7-b212-f850f75066b8" />

Sector 안으로 들어가볼까요? 실제로 데이터를 쓰고 읽는 과정입니다. 각 도메인은 1 bit 와 대응됩니다.

<img width="600" src="https://github.com/user-attachments/assets/84f845cf-ef43-4d52-908d-f024aa88c8c4" />

아래와 같이 자기장 방향을 집중시켜서 각 도메인의 방향을 결정합니다.

<img width="600" src="https://github.com/user-attachments/assets/3a719fc7-f051-4f8d-a0bc-4094d5988e03" />

<img width="600" src="https://github.com/user-attachments/assets/5526c486-7d3c-40c3-ad21-083b7c9be527" />

각 도메인은 여러개의 원자로 이루어져 있는데 방향이 정렬된다... 정도만 알고계시면 충분하실것 같습니다.

<img width="600" src="https://github.com/user-attachments/assets/1975c38c-dc31-4129-9365-70ed3dad4a8e" />

<img width="600" src="https://github.com/user-attachments/assets/b7f690b8-7550-49fc-994a-899f016139e4" />

그러면 각 도메인이 자기장을 발생시키겠죠? 이것을 통해 데이터를 불러오는 겁니다.

<img width="600" src="https://github.com/user-attachments/assets/d1542406-6811-488b-a846-704f85bf8904" />

하지만 여기서 의문점이 생기실 수 있을 것 같습니다. 그래서 윗방향 아랫방향중에 어떤게 1이고 어떤게 0인데? 둘다 아니라고 합니다. 각 도메인은 각자의 자기장보다 방향전환이 있을 때 더 강한 자기장이 발생하는데 이것을 사용합니다.

<img width="600" src="https://github.com/user-attachments/assets/b6ba1c71-7d4d-44e5-9826-3027c500312b" />

이것을 Header가 지나가면서 감지하는데 자기장이 쎄면 저항이 낮아지는 원리가 있는데 그걸 사용해서 감지합니다.

<img width="600" src="https://github.com/user-attachments/assets/ed67ec8f-ad92-48c2-8f59-e39acce106af" />

오케이... 이해가 잘 되시나요? 이정도면 된 것 같습니다. 보너스로 디스크 스케줄링까지 해보도록 하겠습니다.

## HDD 스케줄링

디스크 접근시간은 어떻게 볼 수 있을까요? 크게 세가지가 있습니다.

- 탐색시간(Seek Time): 헤드를 트랙으로 움직이는 데 걸리는 시간
- 회전지연(Rotational latency): 헤드가 원하는 섹터에 도달하기까지 걸리는 시간
- 전송시간(Transfer Time): 데이터의 전송 시간

<img width="600" src="https://github.com/user-attachments/assets/959f7d8d-24ad-4345-8029-5554a3117beb">

여기서 시간이 제일 많이 드는 작업은 바로바로 탐색시간입니다. 맞는 트랙에 Header가 이동하는 것이 가장 긴 시간이 걸립니다. 결국 이 탐색 시간을 줄이는 것이 디스크 스케줄링의 목표라고 할 수 있겠습니다.

### FCFS (First Come First Serve)

큐처럼 FIFO 로 처리하는 겁니다. 그냥 먼저 온 순서대로 처리합니다. 뭔가 좀 아쉽죠? 너무 왔다갔다 하잖아요.

<img width="500" src="https://github.com/user-attachments/assets/5f77a3f6-ba60-420f-b3a3-4fabe27e182b" />


### SSTF (Shortest Seek Time First)

그래서 나왔습니다. 가장 효율적인 방법! 가장 짧은곳을 우선적으로 가는 방법은 어떨까요? 여기서 생각해볼까요? 큐가 주어진다고 가정했을 때 이것보다 빠른 방법이 있을까요?

<img width="500" src="https://github.com/user-attachments/assets/2d2a49dd-7709-433d-8b08-838eda3f568c" />

없겠죠 왜냐하면 가장짧은 이동거리를 이동하는게 목표인데 이거보다 이동거리가 더 짧게 나올수가 없겠죠? 하지만 여기서 문제가 있습니다. 만약에 큐가 계속해서 큰숫자 근처로 들어온다면 작은 숫자는 기약없이 기다려야 합니다. 바로 Starvation 문제가 발생합니다.

### SCAN

이것을 해결하고자 SCAN 방법이 나왔습니다. 나 그냥 왔다갔다하고 지나가는길에 있으면 할께~ 방식입니다. 엘레베이터가 바로 SCAN 방법으로 움직입니다. 올라가는길에 다 태워가죠? 아래의 상황은 `[10, 2, 8, 1]` 이 들어오고 나중에 `[9, 3, 4]` 가 들어오는 상황을 가정했습니다.

<img width="500" src="https://github.com/user-attachments/assets/4843fc99-81b7-4f19-a744-088a699cd798" />

이것도 살짝~ 문제가 있습니다. 뭘까요? 이제 Header는 큐를 신경안쓰고 그냥 왔다갔다 하고 있었다고 생각해봅시다. 아래와 같이 돌아오는 길에 9를 지나갔는데 [9, 3, 4]가 추가되었다면?

<img width="500" src="https://github.com/user-attachments/assets/8cdd3194-a749-4340-bf9d-bbfc752a4060" />

`3, 4`는 문제가 없습니다. `9`의 경우 다시 돌아올 때까지 꽤 긴 시간을 기다려야 할 수 있네요. 눈앞에서 배차간격 긴 버스 놓쳤다고 생각해보세요... 심지어 다음버스가 없어요. 그러면 그 버스 다 돌아올때까지 기다려야 하는데 이게 그 문제입니다.

<img width="500" src="https://github.com/user-attachments/assets/7f06c228-110e-404d-882f-bf8c91c1e32c" />

### C-SCAN (Circular)

그래서 나왔습니다. 한쪽으로만 갑니다. 만약에 끝까지 갔다면 돌아오는길에 처리하는게 아니고 쭉 다시 돌아와서 처음부터 다시 시작합니다.

<img width="500" src="https://github.com/user-attachments/assets/5327845e-1c62-4b73-bddb-642f5b52d805" />

### LOOK, C-LOOK

여기서 만족하지 않았어요. 최대 최소까지만 이동하는 LOOK, C-SCAN과 유사하게 C-LOOK이라는 방법도 있습니다.

<img width="500" src="https://github.com/user-attachments/assets/d7e368a8-14ef-4744-9d5a-ad7b608589d1" />
<img width="500" src="https://github.com/user-attachments/assets/86a6cc1f-3d02-4a01-9e65-ba05220eb751" />

### 끝...! 고생하셨습니다!
