# 32일차

## 라이다와 레이더 데이터의 이해
1. 라이다 원리 : 레이저 펄스를 발사하고, 반사되어 돌아오는 시간 Δt (Time to Flight)을 측정하여 거리를 계산함. D = c * Δt/2
2. 데이터 형태 : 수백만 개의 3차원 점으로 구성된 "포인트 클라우드"를 생성. ( x, y, z, Intensity ) 형태로 Carla Lidar Format임.

3. 레이더 원리 : 전파(Radio wave)를 발사하고, 반사된 파동의 주파수 변화(도플러 효과)를 분석하여 "객체의 거리"와 "상대속도"를 측정함.
4. 데이터 형태 : 탐지된 객체의 목록 출력, ( Velocity, Azimuth, Altitude, Depth ) 형태로 Carla Radar Format임.
-> 레이더는 악천후에 강하고, 도플러 효과를 이용하므로 계산없이 상대 속도가 바로 측정된다.

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
## 객체 검출의 기본원리

## 오프라인 개발 워크플로우와 Carla(카를라) 데이터 활용
