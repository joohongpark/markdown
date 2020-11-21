# OpenGL 기반으로 3d 게임 만들어보기 (cub3d)

cub3d, miniRT는 그래픽 작업을 하는 과제이며 공통적으로 제공하는 MiniLibX 라이브러리를 이용하여 키보드 입력 및 UI로 그래픽을 띄울 수 있게 해 준다.

해당 라이브러리를 사용하기 위해 별도 메뉴얼 파일을 제공한다.

manual 열람법

```
# 예제 : man ./man/man3/(파일명).3
$> man ./man/man3/mix.3
$> man ./man/man3/mix_pixel_put.3
```

mlx.3가 MiniLibX 라이브러리의 전체 메뉴얼이다. 메뉴얼을 보면 MiniLibX는 리눅스/유닉스/맥에서 간단하게 그래픽 창을 띄워주고 기본적인 이벤트 (키보드 등 입력을 말하는 듯)를 사용할 수 있도록 해주는 라이브러리라고 한다.

iniLibX API를 사용하려면 사용하려면 프로젝트에 mlx.h를 추가해야 한다.

