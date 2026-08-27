2026-08-27 데이터 탐색 및 Skeleton 시각화

1. Skeleton 데이터 구조 확인

AI-Hub 피겨스케이팅 데이터의 2D/3D keypoint 데이터를 확인하였다. 한 프레임은 총 26개의 관절로 구성되어 있으며,

2D skeleton: (26, 2) — x, y 좌표
3D skeleton: (26, 3) — x, y, z 좌표

형태로 표현된다.

공식 데이터 설명에서는 26개 관절의 명칭과 좌표 정의는 확인할 수 있었으나, 관절 간 연결 관계(skeleton topology)는 별도로 제공되지 않는 것으로 확인하였다. 따라서 현재 시각화에서는 관절의 해부학적 관계를 기준으로 임시 topology를 정의하여 사용하였다. 모델링 단계에서는 graph adjacency 정의를 별도로 검토할 필요가 있다.

2. 시간축 Skeleton 확인

Annotation의 시작/종료 frame을 기준으로 하나의 동작 구간을 추출하였다. 현재 확인한 샘플은 Frame 50~149로 총 100 frames이며,

2D sequence: (100, 26, 2)
3D sequence: (100, 26, 3)

형태로 구성할 수 있음을 확인하였다.

연속 frame을 animation으로 시각화한 결과 skeleton의 시간적 움직임이 자연스럽게 표현되었으며, 동작 진행에 따른 신체 자세 및 위치 변화를 확인할 수 있었다.

3. Multi-view 데이터 확인

동일 동작에 대해 총 8개 camera view(camera0~camera7)가 존재함을 확인하였다.

현재 샘플에서는 각 camera의 2D keypoint 파일이 다음과 같이 대응되었다.

camera0 → Motion2-1
camera1 → Motion2-2
...
camera7 → Motion2-8

단, 이 파일명 규칙이 전체 데이터셋에서 동일하게 적용되는지는 아직 확인되지 않았으므로, 향후에는 파일명을 직접 가정하지 않고 각 camera 폴더 내부의 CSV를 탐색하여 로드하도록 구성한다.

8개 view를 통합하면 하나의 동작 샘플은 다음 형태로 표현 가능하다.

(8, 100, 26, 2)
 │    │    │   └─ 2D coordinate (x, y)
 │    │    └──── Joint
 │    └───────── Time
 └────────────── View

4. Multi-view 시각화에서 확인한 점

8개 camera의 skeleton을 동일한 시간축으로 동시에 시각화한 결과, 동일한 동작임에도 카메라 시점에 따라 2D skeleton의 형태가 상당히 다르게 관측됨을 확인하였다.

특히 신체 관절이 특정 시점에서 겹쳐 보이거나 자세가 압축되어 보이는 정도가 view에 따라 달랐으며, 넘어지는 구간에서도 신체 구조가 표현되는 형태에 차이가 나타났다.

이는 향후 연구에서 단일 view보다 여러 view의 정보를 상호 보완적으로 활용할 필요성을 검토할 수 있는 정성적 근거가 된다.

단, 현재는 하나의 샘플에 대한 관찰이므로 이를 일반적인 multi-view inconsistency로 단정할 수는 없으며, 추가 샘플에 대한 검증이 필요하다.

5. 다음 확인 사항

다른 점프 종류에서도 8-camera 구조가 동일한지
성공/실패 샘플 모두 동일한 데이터 구조를 갖는지
camera별 frame 수 및 annotation 동기화 여부
3D skeleton과 각 camera의 2D skeleton 관계
전체 데이터에서 CSV naming rule의 일관성
raw/global 좌표와 root-centered 좌표의 차이
최종 skeleton topology 및 graph adjacency 정의
