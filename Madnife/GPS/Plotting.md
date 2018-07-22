
# GPS Plotting using R
R을 활용하여 GPS Data(Latitude, Longitude)를 Google Map에 Dot Plot을 활용하여 Mapping하는 작업 

## Dataset
> Student Life Dataset: Dartmouth Univ.에서 학생들의 생활을 다양한(Sensor, EMA, Survey 등) 형태로 수집한 데이터 

1. Link: http://studentlife.cs.dartmouth.edu/dataset.html
2. Paper: http://ksuweb.kennesaw.edu/~she4/2015Summer/cs7860/Reading/318p3-wang.pdf

 
## Library
> ggplot2, ggmap 활용 (plotly 및 shiny 적용  찾는중)
1. ggplot2: https://ggplot2.tidyverse.org/index.html
2. ggmap: https://github.com/dkahle/ggmap
 
## 특이사항
> Dataset 및 GPS Plotting 과정에서 발생한 특이사항(참고사항)

### Dataset
 1. GPS Data(Latitude, Longitude) 활용
 2. Timestamp를 Date로 변경 (timezone: Eastern Time Zone)
 3. 학생 1명(gps_u00.csv)에게만 예시로 적용

### Plotting
GPS plotting 하면서 5가지 지역을 이동한 것 확인 (5개 GPS Plot 생성)
 1. Main Area: Boston
 2. Sub Area 1(Europe): Paris, London, Venezia
 3. Sub Area 2(America): Washington


## Result

### [1] Main Area: Boston
 
#### Dataset Preprocessing
 1. Timestamp를 Date 형태로 변경
 2. subData(Europe, America) 구분 및 boston data 추출
```
library(ggplot2)
library(ggmap)

gpsData <- read.csv("gps_u00.csv", header = TRUE)
gpsData[,'date'] <- as.Date(as.POSIXct(gpsData$time, tz="EST", origin="1970-01-01")) # timestamp to date 변경

subEurope <- which(gpsData$longitude>-70) # Europe data 구분
subAmerica <- which(gpsData$latitude<40) # Boston 제외한 나머지 Amrica data 구분

boston <- gpsData[-append(subEurope, subAmerica),]
bostonLabel <- boston[c(1,nrow(boston)),] # Boston Data 시작과 끝 지점 추출

```

#### Google Map Setting
 1. 지도 중심(center), 확대 정도(zoom), 지도 형태(maptype) 설정
```
mapImageData <- get_googlemap(
  center = c(lon = -71.460885, lat = 42.896127), # 지도 중심 지점 설정
  zoom = 8, # 확대 정도 설정(3 ~ 21). 
  maptype = c("roadmap") # 지도 형태 설정("terrain", "satellite", "roadmap", and "hybrid")
) 
```

#### Plotting
 1. ggmap에 ggplot을 붙이는 형태로 진행
 2. plot 결과 저장
```
bostonPlot <- ggmap(mapImageData) + 
              geom_point(aes(x = boston$longitude, y = boston$latitude, color=boston$time), 
                         data=boston, size = 3, pch = 20) + 
              ggtitle(paste0("Boston GPS Plot\n", bostonLabel$date[1], " ~ ", bostonLabel$date[2])) + # 타이틀 명칭
              labs(x = "Longitude(경도)", y = "Latitude(위도)") + # x, y축 명칭 설정
              geom_label(aes(x=bostonLabel$longitude, y=bostonLabel$latitude, label = c("S", "E")), 
                         data=bostonLabel, nudge_x = 0.25, nudge_y = 0.2) + # 시작과 끝 포인트 라벨 설정
              theme(legend.position = "none", # 범례 제거
                    plot.title = element_text(family = "serif", face = "bold", hjust = 0.5, size = 13, color = "darkblue")) + # 타이틀 설정
              scale_color_gradient(low = "black", high = "red") # dot plot 색상 설정
  
ggsave("180722_Boston_Plot.png", plot = bostonPlot,  dpi = 600) # plot 저장
```

![Boston Plot Result]

[Boston Plot Result]: 180722_Boston_Plot.png


### [2] Sub Area: Europe
> 작성중입니다...