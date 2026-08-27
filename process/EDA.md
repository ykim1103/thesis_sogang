# 2026-08-27 Skeleton 데이터 탐색 및 Multi-view 시각화

## 1. 목적

AI-Hub 피겨스케이팅 데이터의 실제 구조를 확인하고, 2D/3D Skeleton 데이터를 직접 시각화하여 다음 내용을 검증하였다.

- 2D/3D Keypoint 데이터 구조
- 관절(Joint) 구성
- Annotation 기반 동작 구간 추출
- 시간에 따른 Skeleton 변화
- 8개 Camera의 Multi-view 데이터 구조
- Camera view에 따른 2D Skeleton 차이

---

## 2. Skeleton 데이터 구조 확인

한 프레임의 Skeleton은 총 **26개의 관절(Joint)**로 구성되어 있다.

공식 데이터 설명에서 다음과 같은 관절의 좌표가 제공되는 것을 확인하였다.

- Nose
- LEye / REye
- LEar / REar
- Head
- Neck
- LShoulder / RShoulder
- LElbow / RElbow
- LWrist / RWrist
- Hip
- LHip / RHip
- LKnee / RKnee
- LAnkle / RAnkle
- LBigToe / RBigToe
- LSmallToe / RSmallToe
- LHeel / RHeel

### 2D Skeleton

각 관절은 `(x, y)` 좌표를 가지므로 한 프레임은 다음과 같이 표현된다.

```text
(26, 2)
```

즉,

```text
Joint × Coordinate
  26  ×    2
```

형태이다.

### 3D Skeleton

3D 데이터에서는 각 관절이 `(x, y, z)` 좌표를 가지므로 한 프레임은 다음과 같이 표현된다.

```text
(26, 3)
```

---

## 3. Skeleton Topology

AI-Hub 공식 데이터 설명에서는 **26개 관절의 명칭과 좌표 정의**는 확인할 수 있었으나, 관절 간 연결 관계(Skeleton Topology)는 별도로 확인하지 못하였다.

따라서 현재 시각화에서는 관절의 해부학적 관계를 기준으로 임시 Skeleton Topology를 정의하여 사용하였다.

> **Note**
>
> 현재 사용 중인 Skeleton Edge는 시각화를 위한 임시 정의이다.  
> 향후 GCN 계열 모델 등을 사용할 경우 Graph Adjacency에 직접적인 영향을 미치므로, 모델링 단계에서 표준 Skeleton Topology를 참고하여 별도로 정의할 필요가 있다.

---

## 4. 단일 Frame 2D / 3D 시각화

대표 Frame을 선택하여 2D와 3D Skeleton을 시각화하였다.

2D Skeleton에서는 신체의 팔, 다리, 몸통 등의 구조가 정상적으로 표현되는 것을 확인하였다.

3D Skeleton에서도 동일한 관절 구조가 표현되는 것을 확인하였다.

이를 통해 CSV에서 읽어온 Joint 좌표와 Joint index의 대응이 정상적으로 이루어지고 있음을 확인하였다.

---

## 5. Annotation 기반 Temporal Sequence 추출

Annotation에 정의된 `start_frame`과 `end_frame`을 이용하여 실제 동작 구간만 추출하였다.

현재 확인한 샘플의 Annotation 범위는 다음과 같다.

```text
Start Frame : 50
End Frame   : 149
Total       : 100 Frames
```

따라서 하나의 동작에 대한 2D Skeleton Sequence는 다음과 같다.

```text
(100, 26, 2)
```

3D Skeleton Sequence는 다음과 같다.

```text
(100, 26, 3)
```

각 차원의 의미는 다음과 같다.

```text
2D : Time × Joint × Coordinate
      100 ×  26   ×    2

3D : Time × Joint × Coordinate
      100 ×  26   ×    3
```

---

## 6. 시간에 따른 Skeleton 변화 확인

Frame 50, 75, 100, 125, 149를 선택하여 Skeleton의 시간적 변화를 비교하였다.

각 Frame마다 좌표축을 독립적으로 설정한 경우에는 **자세 변화**를 쉽게 확인할 수 있었다.

반면 모든 Frame에 동일한 좌표 범위를 적용한 경우에는 자세 변화뿐만 아니라 선수가 빙판 위에서 이동하는 **Global Position 변화**까지 확인할 수 있었다.

따라서 현재 좌표에는 다음 두 종류의 정보가 함께 포함되어 있다.

```text
Skeleton Coordinate
        │
        ├── Local Pose 변화
        │
        └── Global Position 변화
```

향후 모델링 과정에서는 원본 좌표를 그대로 사용하는 방법과 Hip 등의 Root Joint를 기준으로 정규화하는 방법을 비교할 필요가 있다.

---

## 7. Skeleton Animation

Annotation 구간의 100 Frames를 연속적으로 시각화하여 Skeleton Animation을 생성하였다.

Animation을 통해 단일 Frame만 확인했을 때보다 동작의 진행 과정과 자세 변화가 자연스럽게 표현되는 것을 확인하였다.

MP4 형태로 Skeleton Animation을 저장할 수 있도록 FFmpeg 환경도 구성하였다.

---

## 8. Multi-view 데이터 구조 확인

동일한 동작에 대해 총 **8개의 Camera View**가 존재하는 것을 확인하였다.

```text
camera0
camera1
camera2
camera3
camera4
camera5
camera6
camera7
```

현재 확인한 샘플에서는 각 Camera와 CSV 파일이 다음과 같이 대응되었다.

```text
camera0 → Motion2-1 - 1of2.csv
camera1 → Motion2-2 - 1of2.csv
camera2 → Motion2-3 - 1of2.csv
camera3 → Motion2-4 - 1of2.csv
camera4 → Motion2-5 - 1of2.csv
camera5 → Motion2-6 - 1of2.csv
camera6 → Motion2-7 - 1of2.csv
camera7 → Motion2-8 - 1of2.csv
```

단, 위 Naming Rule이 전체 데이터셋에 동일하게 적용되는지는 아직 확인되지 않았다.

따라서 Loader에서는 CSV 파일명을 직접 생성하지 않고 각 `cameraN/local_keypoints/` 디렉터리 내부의 CSV를 탐색하여 불러오도록 구성하였다.

---

## 9. Multi-view Sequence 구성

8개 Camera의 동일한 Annotation 구간을 하나의 배열로 통합하였다.

최종 2D Multi-view Skeleton의 Shape은 다음과 같다.

```text
(8, 100, 26, 2)
```

각 차원의 의미는 다음과 같다.

```text
(8, 100, 26, 2)
 │    │    │   │
 │    │    │   └── Coordinate (x, y)
 │    │    └────── Joint
 │    └─────────── Time
 └──────────────── View
```

즉 하나의 동작 샘플을

```text
View × Time × Joint × Coordinate
```

형태로 표현할 수 있음을 확인하였다.

---

## 10. 8-View 동시 시각화

8개 Camera의 Skeleton Sequence를 `2 × 4` 형태로 배치하여 동일한 Frame을 동시에 재생하였다.

```text
Camera 0 | Camera 1 | Camera 2 | Camera 3
---------|----------|----------|---------
Camera 4 | Camera 5 | Camera 6 | Camera 7
```

동일한 실제 동작을 촬영한 데이터임에도 **Camera View에 따라 2D Skeleton의 형태가 상당히 다르게 관측되는 것**을 확인하였다.

특정 시점에서는 일부 View에서 신체 관절이 서로 가까워지거나 겹쳐 보이는 반면, 다른 View에서는 해당 관절의 상대적인 위치가 보다 명확하게 표현되었다.

넘어지는 구간에서도 Camera View에 따라 신체 구조가 2D 공간에 투영되는 형태에 차이가 나타났다.

이는 향후 단일 View에 의존하기보다 **여러 View의 정보를 상호 보완적으로 활용하는 방법**을 검토할 필요성을 보여주는 정성적 사례가 될 수 있다.

단, 현재 결과는 하나의 동작 샘플에 대한 관찰이므로 일반적인 **Multi-view Inconsistency**의 존재를 입증한 결과로 해석할 수는 없다. 향후 다양한 동작 및 샘플을 이용한 추가 검증이 필요하다.

---

## 11. 현재까지 확인한 데이터 흐름

```text
Raw CSV
   │
   ├── 2D Keypoints
   │       ↓
   │   (26, 2)
   │
   └── 3D Keypoints
           ↓
       (26, 3)

            ↓

Annotation 기반 동작 구간 추출

            ↓

Temporal Sequence

2D → (T, 26, 2)
3D → (T, 26, 3)

            ↓

8-Camera 통합

            ↓

Multi-view 2D Sequence
(V, T, 26, 2)

현재 Sample:
(8, 100, 26, 2)
```
<img width="1600" height="800" alt="Image" src="https://github.com/user-attachments/assets/13abcf76-a279-446d-9eba-07becd984995" />
---

## 12. 향후 확인 사항

현재 샘플에 대한 기본적인 Skeleton 데이터 구조 및 시각화는 완료하였다.

다음 단계에서는 다음 항목을 추가로 확인할 예정이다.

- 다른 점프 종류에서도 동일한 8-Camera 구조를 가지는지 확인
- 성공/실패 샘플 간 데이터 구조 확인
- Camera별 전체 Frame 수 일치 여부 확인
- Camera 간 Frame Synchronization 확인
- CSV Naming Rule의 전체 데이터셋 일관성 확인
- 3D Skeleton과 Multi-view 2D Skeleton의 관계 확인
- Global Coordinate와 Root-centered Coordinate 비교
- Skeleton Topology 및 Graph Adjacency 정의
- 다양한 샘플에서 Multi-view 차이 정성적/정량적 분석

---

## 13. 현재 단계 결론

이번 데이터 탐색을 통해 단일 Frame의 2D/3D Skeleton 확인에서 시작하여, 시간축을 포함한 Skeleton Sequence와 8-Camera Multi-view Sequence까지 실제 데이터로 구성할 수 있음을 확인하였다.

특히 하나의 동작을 다음과 같은 형태로 구성할 수 있음을 확인하였다.

```text
Single Frame
     ↓
Temporal Skeleton
     ↓
Multi-view Temporal Skeleton

(8 Views × 100 Frames × 26 Joints × 2 Coordinates)
```

또한 동일한 동작이라도 Camera View에 따라 2D Skeleton의 관측 형태가 달라지는 현상을 정성적으로 확인하였다.

향후에는 이러한 View 간 차이가 다양한 동작과 샘플에서도 반복적으로 나타나는지 확인하고, Multi-view 정보를 활용하는 방법을 구체화할 예정이다.
