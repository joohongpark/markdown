# OpenGL 기반으로 3d 게임 만들어보기 (cub3d)

cub3d, miniRT는 그래픽 작업을 하는 과제이며 공통적으로 제공하는 MiniLibX 라이브러리를 이용하여 키보드 입력 및 UI로 그래픽을 띄울 수 있게 해 준다.

해당 라이브러리를 사용하기 위해 별도 메뉴얼 파일을 제공한다.

## manual 열람법

```
# 예제 : man ./man/man3/(파일명).3
$> man ./man/man3/mix.3
$> man ./man/man3/mix_pixel_put.3
```

mlx.3가 MiniLibX 라이브러리의 전체 메뉴얼이다. 메뉴얼을 보면 MiniLibX는 리눅스/유닉스/맥에서 간단하게 그래픽 창을 띄워주고 기본적인 이벤트 (키보드 등 입력을 말하는 듯)를 사용할 수 있도록 해주는 라이브러리라고 한다.

MiniLibX API를 사용하려면 사용하려면 프로젝트에 mlx.h를 추가해야 한다.

mlx_init 함수를 이용해서 그래픽을 사용할 준비를 한다. 이후로 크게 4가지 함수를 이용해서 그래픽을 만들거나 마우스, 키보드 입력을 받는다.

- mlx_new_window : 윈도우 제어
- mlx_pixel_put : 윈도우 내부에 뭔가를 그림
- mlx_new_image : 윈도우 내부에 이미지를 그림
- mlx_loop : 키보드, 마우스 입력을 다룸

## 링킹 방법

링킹은 컴파일 작업을 할 때 다른 기계어로 번역된 파일을 합치는 것을 말한다.

예를 들어 main.c 와 prog.c 파일이 하나의 프로젝트라 하면 위 파일은 기계어로 바뀌고 (컴파일) 바뀐 파일을 하나의 실행파일로 바꾸는 것을 링킹이라 한다.

MiniLibX은 별도의 라이브러리 이므로 현재 사용하고자 하는 프로젝트에 추가시켜야 하는데 이 작업을 링킹이라고 한다.

컴파일을 할 때 다음 옵션을 넣는다.

```makefile
# 맥OS에서
-lmlx -framework OpenGL -framework AppKit -lz -L(라이브러리 경로)
# 유닉스나 리눅스에서
-lmlx -lXext -lX11 -L(라이브러리 경로)
```

## 환경 구성 해보기 및 창 띄워보기

Makefile 작성을 해본다.

```makefile
MMSPATH = ./libs/minilibx_mms_20200219
CLFLAGS = -lmlx -framework OpenGL -framework AppKit -lz

$(NAME): $(OBJS)
	make clean -C ${MMSPATH}
	make all -C ${MMSPATH}
	$(COMPILER) $(CFLAGS) $(OBJS) -I$(HEADERPATH) -L$(MMSPATH) $(CLFLAGS) -o $(NAME)
```

make의 -C 옵션으로 MiniLibX 라이브러리를 별도로 컴파일 한 후에 현재 프로젝트에 생성된 라이브러리를 링크해야 한다.

하지만 해당 작업 중 오류가 발생했다.

```shell
$> ./cub3d 
dyld: Library not loaded: libmlx.dylib
  Referenced from: /Users/joohongpark/Documents/code/42/42cursus/cub3d/./cub3d
  Reason: image not found
zsh: abort      ./cub3d
```

기존에 동적 라이브러리를 생성하는 과제는 확장자가 *.a 였다. 또 기존에 cub3d 과제에서 제공해 준 라이브러리도 *.a로 라이브러리가 생성되는 것으로 알고 있었다. 하지만 과제 수행을 위해 빌드해 보니 라이브러리 확장자가 *.dylib이었다. 슬랙에서 관련 문제를 검색해 보니 실행 바이너리에 해당 파일을 두거나 install_name_tool 이라는 명령어를 이용해 링크를 시켜야 한다고 한다.

나는 install_name_tool을 사용해서 해당 링크파일을 적용하려 했지만 원인 모를 오류가 발생해 (현재 install_name_tool와 동적 라이브러리 링크에 대해 완벽한 이해가 없기도 하고) 실행 바이너리에 해당 라이브러리를 복사하는 것으로 임시로 해결하기로 하였다.

실행한 테스트 코드는 다음과 같다.

```c
#include "cub3d.h"

int				main(void)
{
	void		*test;
	void		*test1;

	test = mlx_init();
	test1 = mlx_new_window(test, 300, 300, "hello");
	mlx_loop(test);
	return (0);
}
```

man 페이지를 참조해 mlx_init 함수와 mlx_new_window 함수를 사용했다. mlx_loop 함수를 이용해 창이 닫히지 않게 한다.

이제 이 코드를 바탕으로 창에 3d 그림을 출력해야 할 것이다...

## 키 입력 받기

man 페이지로 mlx_loop를 보면 hook 함수로 키 입력을 받는 함수들이 설명되어 있다.

```c

int				ft_key_press(int code, char *str)
{
	printf("code : %02x at %s\n", code, str);
	return (0);
}

int				ft_mouse_press(int code, int x, int y, char *str)
{
	printf("code : %02x(%d, %d) at %s\n", code, x, y, str);
	return (0);
}

int				ft_test(char *str)
{
	printf("code : X at %s\n",  str);
	return (0);
}

int				main(void)
{
	void		*test;
	void		*test1;

	test = mlx_init();
	test1 = mlx_new_window(test, 300, 300, "hello");
	mlx_key_hook(test1, &ft_key_press, "key");
	mlx_mouse_hook(test1, &ft_mouse_press, "mouse");
	mlx_expose_hook(test1, &ft_test, "expose");
	mlx_loop_hook(test1, &ft_test, "loop");
	mlx_loop(test);
	return (0);
}
```

hoop 함수는 해당 이벤트가 발생하면 특정 함수에 인수를 입력시켜 실행하도록 한다.

man 페이지에 설명되지 않은 함수 중 mlx_hook 이라는 함수가 있다고 한다. 하지만 현재까지 필요성을 못 느껴 추후에 필요성을 느낄 때 다시 사용할 계획이다. 해당 함수를 이용하면 창 닫기도 구현할 수 있다는 듯 하다.

## 윈도우에 무언가를 출력하기

띄운 창에 무엇인가를 출력해본다.

```c
#include "cub3d.h"

int				main(void)
{
	void		*test;
	void		*test1;
	void		*img;
	void		*img_xpm;

	int			*img_data;
	int			bits_per_pixel;
	int			size_line;
	int			endian;

	int			xy[2];

	test = mlx_init();
	test1 = mlx_new_window(test, 600, 600, "hello");

	/* 1. 픽셀을 직접 지정해서 출력 */
	// 이미지의 가로 세로 크기만큼 메모리를 할당한다.
	img = mlx_new_image(test, 256, 256);
	// 생성된 이미지의 픽셀 정보를 가져온다.
		// bits_per_pixel 에는 픽셀 하나당 차지하는 비트수가 들어가며 (32bit) 
		// size_line 에는 이미지의 한 줄을 저장하는 데 사용되는 바이트 수가 들어간다.
		// endian에는 해당 픽셀 컬러가 저장될 때 리틀 인디안인지 빅 인디안인지 여부를 나타낸다.
		// mlx_get_data_addr 함수는 char 형 포인터를 리턴하지만 int 형 포인터로 캐스팅해야 한다.
	img_data = (int *)mlx_get_data_addr(img, &bits_per_pixel, &size_line, &endian);
	for (int i = 0; i < 256 * 256; i++)
		img_data[i] = i;
	printf("%d, %d, %d\n", bits_per_pixel, size_line, endian);

	/* 2. xpm 파일을 불러와서 출력 */
	img_xpm = mlx_xpm_file_to_image(test, "./test.xpm", &xy[0], &xy[1]);
	
	mlx_put_image_to_window(test, test1, img, 0, 0);
	mlx_put_image_to_window(test, test1, img_xpm, 256, 256);
	mlx_loop(test);
	return (0);
}
```

xpm 파일은 MiniLibX에서 사용하는 그림 파일 포맷이다. 자세한 건 아직 잘 모르는 상태에서 우선 그림파일 출력부터 해본다.

mlx_new_image 혹은 mlx_xpm_file_to_image 함수를 이용해서 이미지를 생성한다.

생성한 후 mlx_put_image_to_window 함수를 이용해 지정한 위치에 출력한다.

작성하면서도 아직 뭐가 뭔지 잘 몰라 다른 예제들을 코딩하면서 감을 익힐 것이다.