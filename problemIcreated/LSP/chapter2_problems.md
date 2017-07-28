# 2단원 자문자답

## 1. 2장에서 소개되는 시스템 콜의 이름을 전부 작성하시오.

	* 파일 열기, 쓰기 및 읽기 
		* open(), creat(), close(), read(), write()
	* 동기식 입출력
		* sync(), fsync(), fdatasync()
	* 다중 입출력
		* select(), pselect(), poll(), epoll() 그리고 timeval과 timespec에 대해서 알고 넘어가자.
	* 파일 탐색하기
		* lseek(), pread(), pwrite()
	* 파일 잘라내기
		* ftruncate(), 
	* 에러 처리
		* perror(), strerr(), extern int errno

## 2. 1번 문제에서 나온 모든 시스템 콜에 대한 API를 작성하고 쓰임새에 대해서 간략하게 서술하시오.

* open(): 시스템 콜을 사용해서 파일을 열고 파일 디스크립터를 얻는다.


API는 다음과 같다.
```{.c}
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

int open(const char *name, int flag);
int open(const char *name, int flag, mode_t mode);
```
flag인자는 O\_RDONLY, O\_WRONLY, O\_RDWR중 하나를 포함해야 한다.  
각 플래그는 일기 전용모드, 쓰기 전용 모드, 읽기와 쓰기 모드로 파일을 열도록 요청한다.


예를 들어 다음 코드는 읽기 전용 모드로 /home/kidd/madagascar 파일을 연다.
```{.c}
int fd;

fd = open("/home/kidd/madagascar", O_RDONLY);

if (fd == -1)
	// 에러
```

mode 인자에는 S\_접두어가 붙는 파일 권한을 제어하는(그리고 이진연산이 가능한) 인자를 줄 수 있다.
또는 기술적으로는 이식성이 없는 방식이지만, 0644나 0700같은 8진수 값을 줄 수도 있다. 


* creat(): O\_RDONLY, O\_CREAT, O\_TRUNC 조합을 open()의 flag인자에 넣어서 실행하는 시스템 콜
	* 자세한 설명은 생략한다.

* read(): 호출할 때마다 fd가 참조하는 파일의 현재 파일 오프셋에서 len바이트 만큼 buf로 읽어들인다.
	* 성공하면 buf에 쓴 바이트 숫자를 반환한다. 실패하면 -1을 반환하며, errno를 설정한다.
	* 파일 오프셋은 fd에서 읽은 바이트 크기만큼 전진한다.(예를 들면 문자 디바이스 파일처럼)
	* fd가 표현하는 객체에 탐색 기능이 없다면 읽기 작업을 항상 "현재" 위치에서 일어난다.
	
API는 다음과 같다.
```{.c}
#include <unistd.h>

ssize_t read(int fd, void *buf, size_t len);
```

단순한 구현은 다음과 같다.
```{.c}
unsigned long word;
ssize_t nr;

// fd에서 몇 바이트를 읽어서 word에 저장한다.
nr = read(fd, &word, sizeof(unsigned long));
if(nr == -1)
	// error
```





## 3. flags나 권한인자들에 대해서 알아보고 어떤 종류가 있으며 나머지는 어디서 쓰이는지 알아보시오.
