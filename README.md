# 32일차

## 라이다와 레이더 데이터의 이해
1. 라이다 원리 : 레이저 펄스를 발사하고, 반사되어 돌아오는 시간 Δt (Time to Flight)을 측정하여 "거리"를 계산함. D = c * Δt/2
2. 데이터 형태 : 수백만 개의 3차원 점으로 구성된 "포인트 클라우드"를 생성. ( x, y, z, Intensity ) 형태로 Carla Lidar Format임.
-> 라이다는 정밀한 형태지만, 속도 측정엔 약하다.

3. 레이더 원리 : 전파(Radio wave)를 발사하고, 반사된 파동의 주파수 변화(도플러 효과)를 분석하여 "객체의 거리"와 "상대속도"를 측정함.
4. 데이터 형태 : 탐지된 객체의 목록 출력, ( Velocity, Azimuth, Altitude, Depth ) 형태로 Carla Radar Format임.
-> 레이더는 악천후에 강하고, 도플러 효과를 이용하므로 계산없이 상대 속도가 바로 측정된다.

**결론 : 라이다의 정말한 형태와, 레이더의 속도/악천후 장점을 융합하자.**<br>

실습 : lidar의 포인트 클라우드와, Radar의 탐지 객체 목록을 2d평면에 함께 시각화하기.<br>
<img width="857" height="817" alt="image" src="https://github.com/user-attachments/assets/c1f5ec0f-2b69-4d1c-9f82-f6699f7142ed" /><br>
```python
plt.scatter(lidar_data[:, 0], lidar_data[:, 1], s=5, label='Lidar Points')  # 0번째 열벡터 x, 1번째 열벡터 y 선택
```

Radar에서는 **x = depth * cos(azimuth), y = depth * sin(azimuth) 공식**을 사용하여 변환해야 함.<br>
```python
radar_x = radar_data[:, 3] * np.cos(radar_data[:, 1])
radar_y = radar_data[:, 3] * np.sin(radar_data[:, 1])
```
## 센서 데이터로부터 객체를 탐지하는 기본원리
- 객체 검출의 기본 파이프라인 ( 전처리 -> 군집화 -> 분류 )
- 임계값(Thresholding) 과 필터링(Filtering)
- 관심영역(ROI, Region Of Interesting) 설정

실습 : 라이다 데이터에서 불필요한 점을 제거하고, ROI영역의 점들만 추출하는 코드 작성하기.<br>
<img width="850" height="821" alt="image" src="https://github.com/user-attachments/assets/3ee4db40-e986-41e0-841a-09fd01380acd" /><br>
1.  **지면 제거:** Z 좌표가 -1.0m보다 높은 포인트들만 남김. Thresholding 값이 -1.0인 것.
2.  **ROI 설정:** 차량 전방 18m ~ 22m, 좌우 2m 영역 내의 포인트만 남김. Thresholding 값이 18~22 인 것.<br> 
-> 여러 조건을 & 연산자로 결합하기.
```python
points_no_ground = lidar_data[lidar_data[:, 2] > -1.0]  # z좌표 필터링

roi_points = points_no_ground[
     (points_no_ground[:, 0] > 18) & (points_no_ground[:, 0] < 22) &  # x 좌표 필터링
     (points_no_ground[:, 1] > -2) & (points_no_ground[:, 1] < 2)     # y 좌표 필터링
]
```
## 오프라인 개발 워크플로우와 Carla(카를라) 데이터 활용
- Carla 시뮬레이터는 VRAM 8기가 이상, ssd 용량도 많이 필요한 고사양의 compressive 실습이라 패스.
- 알고리즘 핵심은 약간의 포장(Wrapping)을 거쳐, 실제 시스템의 소프트웨어 부품(ROS2 노드)이 되는것.
- colab에서 작성한 경우에도, 파일 로드와 시각화만 다를뿐이지 핵심 로직은 변하지 않는다.

- 내일부턴 RadarSimPy 라이브러리로 동적인 데이터들(움직이는 관찰자, 차량 등)을 다뤄볼것.
- Carla와 동일한 센서 파라미터들(FOV, 해상도, 노이즈) 설정이 가능한 시뮬레이션이고, colab에서도 가능하다!
