- [出错信息](#%E5%87%BA%E9%94%99%E4%BF%A1%E6%81%AF)
- [标准输入输出](#%E6%A0%87%E5%87%86%E8%BE%93%E5%85%A5%E8%BE%93%E5%87%BA)
- [时间处理](#%E6%97%B6%E9%97%B4%E5%A4%84%E7%90%86)
- [内存分配](#%E5%86%85%E5%AD%98%E5%88%86%E9%85%8D)
- [文件操作（非缓冲）](#%E6%96%87%E4%BB%B6%E6%93%8D%E4%BD%9C%E9%9D%9E%E7%BC%93%E5%86%B2)
    - [关于几个数据类型:](#%E5%85%B3%E4%BA%8E%E5%87%A0%E4%B8%AA%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)
- [目录操作](#%E7%9B%AE%E5%BD%95%E6%93%8D%E4%BD%9C)
- [文件属性](#%E6%96%87%E4%BB%B6%E5%B1%9E%E6%80%A7)
- [学习中写的代码](#%E5%AD%A6%E4%B9%A0%E4%B8%AD%E5%86%99%E7%9A%84%E4%BB%A3%E7%A0%81)
  - [时间相关](#%E6%97%B6%E9%97%B4%E7%9B%B8%E5%85%B3)
  - [文件相关](#%E6%96%87%E4%BB%B6%E7%9B%B8%E5%85%B3)
  - [目录相关](#%E7%9B%AE%E5%BD%95%E7%9B%B8%E5%85%B3)
  - [文件属性相关](#%E6%96%87%E4%BB%B6%E5%B1%9E%E6%80%A7%E7%9B%B8%E5%85%B3)

# 出错信息
` errno.h ` 中定义一个全局的变量` extern int errno; `,当一个API函数出错时，这个全局变量就会被设置成出错信息编号。
* ` char * strerror(int errnum); `  在` string.h `中定义，根据提供的错误编号，返回对应的错误信息。
* ` void perror(const char * msg); ` 在` stdio.h `中定义，根据全局变量` errno `的值，打印相应的错误信息，并在此信息前加上` msg `作为前置提示信息。

# 标准输入输出
* 头文件` stdio.h `
* 输出：` int printf(const char * format, ...); `
* 输入：` int scanf(const char * format, ...); `
> ` scanf("%s", string) `从标准输入中读入字符串时，以` whitespace `为字符结束标志，` whitespace `包括空格、制表符、回车、换行等符号。**如何读入包括空格的字符串，目前还不知道**。

# 时间处理
* 头文件` time.h `
* 核心结构` struct tm `、` struct timeval `、` struct timezone `
```c
struct tm
{
  int tm_sec;			/* Seconds.	[0-60] (1 leap second) */
  int tm_min;			/* Minutes.	[0-59] */
  int tm_hour;			/* Hours.	[0-23] */
  int tm_mday;			/* Day.		[1-31] */
  int tm_mon;			/* Month.	[0-11] */
  int tm_year;			/* Year	- 1900.  */
  int tm_wday;			/* Day of week.	[0-6] */
  int tm_yday;			/* Days in year.[0-365]	*/
  int tm_isdst;			/* DST.		[-1/0/1]*/
};
```
```c
struct timeval {
   time_t      tv_sec;     /* seconds */
   suseconds_t tv_usec;    /* microseconds */
};
```
```c
struct timezone {
   int tz_minuteswest;     /* minutes west of Greenwich */
   int tz_dsttime;         /* type of DST correction */
};
```
* ` time_t time (time_t *t_ptr) ` 调用系统调用返回从1970年1月1日0时0分0秒至调用时的秒数，可直接返回，也可通过` time_t `的指针返回，两种方式返回的值是一样的。` time_t `就是` typedef unsigned long `。
* ` time_t `和` struct tm `之间的转换：    
` struct tm *gmtime (const time_t *timer) `  //` time_t `=>` struct tm ` 返回的国际标准时间       
` struct tm *localtime (const time_t *timer) `  //` time_t `=>` struct tm ` 返回的是本地时间    
` time_t mktime (struct tm *tp) `  //` struct tm `=>` time_t `
* ` struct tm `转换为时间字符串   
` char *asctime (const struct tm *__tp) `
* ` time_t `转换为时间字符串   
` char *ctime (const time_t *__timer) `
> ` asctime_r() `、` ctime_r() `这两个函数都可以再加上第二个参数` char * buf `，但要确保有足够的空间保存这个字符串。
* 挂起进程  
  ```c
  #include <unistd.h>
  unsigned int sleep(unsigned int seconds);
  ```


# 内存分配
* 头文件` stdlib.h `
* 主要的4个函数    
` void * malloc(size_t size) `  //分配` size `个字节的内存块，未初始化   
` void * calloc(size_t nmemb, size_t size) `  //分配` nmemb `个大小为` size `字节的内存块，初始化为` 0 `   
` void * realooc(void * buf, size_t newsize) `  //重新分配` newsize `字节大小的内存块，并将原` buf `中的内容迁移过来，新的大小可以比原来的大小大，也可以比原来的大小小    
` void free(void * buf) `  //释放掉` buf `指向的内存块

# 文件操作（非缓冲）
* 七种文件类型
    * 普通文件
    * 目录文件
    * 块设备文件 
    * 字符设备文件
    * 命名管道文件
    * 套接字文件
    * 符号链接文件
* 文件描述符` file descriptor `，一个非负整数，它其实是进程的文件描述表的索引，前三个已固定，并在进程启动的时候自动打开，分别是：
    ```c
    #define	STDIN_FILENO	0	/* Standard input.  */
    #define	STDOUT_FILENO	1	/* Standard output.  */
    #define	STDERR_FILENO	2	/* Standard error output.  */
    ```
* 头文件
    * ` fcntl.h `       包含` open() `、` create() `函数
    * ` unistd.h `      包含` read() `、` write() `、` lseek() `、` close() `等函数
* 打开文件  
    ` int open(const char * pathname, int flags) `   
    ` int open(const char * pathname, int flags, mode_t mode) `
    > **` flags `**: 三个主要标志：` O_RDONLY(只读) `、` O_WRONLY(只写) `、` O_RDWR(读定) `，三者取其一。还有一附加标志，通过` | `和主要标志组合起来，常用的有：` O_CREATE `(文件不存在则创建)、` O_APPEND `(进行写操作时，写到文件末尾)、` O_TRUNC `(不管以什么方式打开，文件存在则截断为长度0)等等   
    > 
    >**` mode `**: 指定创建文件的模式，其值如下：  
    >    * ` S_IRUSR	 --Read by owner. `  
    >    * ` S_IWUSR     --Write by owner. `  
    >    * ` S_IXUSR	 --Execute by owner. `  
    >    * ` S_IRWXU	 --S_IRUSR | S_IWUSR | S_IXUSR `  
    >    * ` S_IRGRP	 --Read by group. `  
    >    * ` S_IWGRP	 --Write by group. `  
    >    * ` S_IXGRP	 --Execute by group. `  
    >    * ` S_IRWXG	 --S_IRGRP | S_IWGRP | S_IXGRP `  
    >    * ` S_IROTH	 --Read by others. `  
    >    * ` S_IWOTH	 --Write by others. `  
    >    * ` S_IXOTH	 --Execute by others. `  
    >    * ` S_IRWXO	 --S_IROTH | S_IWOTH | S_IXOTH `

* 创建文件` int create(char * pathname, mode_t mode) `
    > ` mode `与` open() `函数完全相同  
    > ` create() `其实等同于` int open(char * pathname, O_WRONLY | O_CREATE | O_TRUNC, mode_t mode) `

* 关闭文件` int close(int fd) `
    > 关闭已打开的文件，成功返回` 0 `，否则返回` －1 `

* 读文件` ssize_t read(int fd, char * buf, size_t cout) ` 
    > 从` fd `指定的文件当前位置开始，读取` count `字节的数据放入到` buf `中，`count`的大小一般与`buf`的大小一致。   
    > 函数返回实际读取的数据长度，一般与`count`一致，到文件尾时，返回实际的大小，下一次再调用`read()`则返回`0`，出错返回`－1`。

* 写文件` ssize_t write(int fd, char * buf, size_t count) `
    > 从` fd `指定的文件当前位置开始，将`buf`中的` count `字节数据写入文件中，`count`的大小一般与`buf`的大小一致。   
    > 函数返回实际写入文件的数据长度，一般与`count`一致，否则出错（如磁盘已满等），失败返回`－1`。

* 移动文件位置` off_t lseek(int fd, off_t offset, int whence) `
    > `whence` -- 指定从何处移动文件位置，有以下三种取值：  
    > * ` SEEK_SET  -- Beginning of file`  
    > * ` SEEK_CUR  -- Current position of file`  
    > * ` SEEK_END  -- End of file`  

### 关于几个数据类型:  
 | 类型 | 定义 |  
 |:----|:----|  
 | size_t|typedef unsigned long |  
 | ssize_t|typedef signed long   |  
 | off_t  |typedef signed long   |  
 | mode_t |typedef unsigned int  |  

# 目录操作
* 创建目录`mkdir()`
  ```c
  #include <sys/stat.h>
  int mkdir(const char * pathname, mode_t mode);
  ```
  让人没想到的是，这个函数不在`unistd.h`中，而是在`sys/stat.h`中。但这个头文件的注释是这样的
  ```c
  /* POSIX Standard: 5.6 File Characteristics	<sys/stat.h> */
  ```
  > `mode`参数与文件操作函数的mode是相同的意义，但要注意，对目录来说**最少要设置一个执行权限位**以允许用户访问该目录中的文件。  
  `pathname`指定的新目录的父目录必须存在，并且没有权限问题，否则创建失败，返回`-1`。
* 删除目录`rmdir()`
  ```c
  #include <unistd.h>
  int rmdir(const char * pathname);
  ```
  > 调用成功的条件：目录是空目录，且没有权限问题。失败返回`-1`。
* 获取当前工作目录`getcwd()`   
  当前工作目录（`current work directory`），shell下查看当前工作目录的使命是`pwd`（`print work directory`），切换当前工作目录的命令是`cd`（`change directory`）。
  ```c
  #include <unistd.h>
  char * getcwd(char * buf, size_t size);
  ```
  > `buf`用来存在当前工作目录字符串的缓冲区，`size`是缓冲区的长度。  
  函数返回的是指向当前工作目录字符串的指针，失败返回`NULL`（`0`）。`PATH_MAX`定义了路径名的最大长度，一般是`4096`，`NAME_MAX`定义了文件名的最大长度，一般是`255`。
* 切换当前工作目录`chdir()`
  ```c
  #include <unistd.h>
  int chdir(const char * pathname);
  ```
  > 注意切换的是调用`chdir()`函数进程的当前工作目录。`pathname`指定的路径长度不能超过`PATH_MAX`，其路径分量不超过`NAME_MAX`。成功返回`0`，失败返回`-1`。
* 打开、关闭和读取目录
  ```c
  #include <dirent.h>
  DIR * opendir(const char * pathname);
  int closedir(DIR * dp);
  struct dirent * readdir(DIR * dp);
  ```
  > * `DIR`（`The data type of directory stream objects. The actual structure is opaque to users.`），可以简单的视为文件描述符指针。  
  > * `struct dirent`定义如下：
  > ```c
  > struct dirent
  > {
  >   ino_t d_ino;                  /* Inode number */
  >   off_t d_off;
  >   unsigned short int d_reclen;  /* Length of this record */
  >   unsigned char d_type;         /* Type of file; not supported by all filesystem types */
  >   char d_name[256];             /* Null-terminated filename */
  > };
  > /* The only fields in the dirent structure that are mandated by POSIX.1 are d_name and d_ino. The other fields are unstandardized, and not present on all systems. */
  > /* d_type的取值  */
  > enum
  > {
  >   DT_UNKNOWN = 0,
  >   DT_FIFO = 1,
  >   DT_CHR = 2,
  >   DT_DIR = 4,
  >   DT_BLK = 6,
  >   DT_REG = 8,
  >   DT_LNK = 10,
  >   DT_SOCK = 12,
  >   DT_WHT = 14
  > };
  > ```
  > * `opendir()`与`open()`不同，它不能创建目录，如果指定的目录不存在，则失败。该函数成功返回一个`DIR`类型的指针，失败返回`NULL`。
  > * `readdir()`每调用一次，返回`DIR * dp`指定的目录文件中的一项内容(在`struct dirent`中)，直至读到目录尾时返回`NULL`。出错时也返回`NULL`。

# 文件属性
* 头文件`<sys/stat.h>`
* 核心结构体`struct stat`
  ```c
  struct stat {
    dev_t     st_dev;         /* ID of device containing file */
    ino_t     st_ino;         /* Inode number */
    mode_t    st_mode;        /* File type and mode */
    nlink_t   st_nlink;       /* Number of hard links */
    uid_t     st_uid;         /* User ID of owner */
    gid_t     st_gid;         /* Group ID of owner */
    dev_t     st_rdev;        /* Device ID (if special file) */
    off_t     st_size;        /* Total size, in bytes */
    blksize_t st_blksize;     /* Block size for filesystem I/O */
    blkcnt_t  st_blocks;      /* Number of 512B blocks allocated */

    /* Since Linux 2.6, the kernel supports nanosecond precision for the following timestamp fields. For the details before Linux 2.6, see NOTES. */

    struct timespec st_atim;  /* Time of last access */
    struct timespec st_mtim;  /* Time of last modification */
    struct timespec st_ctim;  /* Time of last status change */

    #define st_atime st_atim.tv_sec      /* Backward compatibility */
    #define st_mtime st_mtim.tv_sec
    #define st_ctime st_ctim.tv_sec
  };
  ```
  > `st_mode`中既保存有文件的类型，也保存了文件的权限，其中低9位分别指出`owner`、`group`、`others`的读、定和执行权限，高位部分指出文件的类型。**注意：结构中并不包括文件名**
* 获取文件属性
  ```c
  #include <sys/stat.h>
  int stat(const char * pathname, struct stat * sbuf);
  int fstat(int fd, struct stat * sbuf);
  int lstat(const char * pathname, struct stat * sbuf);
  ```
  > `stat()`和`lstat()`获取`pathname`指定的文件的属性，其区在于对符号链接文件（`LINK`），`lstat()`返回该符号链接的属性，而`stat()`返回该符号链接所引用的文件的属性。`fstat()`获取文件描述符fd指定的文件的属性。获取的属性信息放入`struct stat * sbuf`中。
* 关于文件类型的宏
  ```c
  #define	__S_ISTYPE(mode, mask)	(((mode) & __S_IFMT) == (mask))
  #define	S_ISDIR(mode)	 __S_ISTYPE((mode), __S_IFDIR)
  #define	S_ISCHR(mode)	 __S_ISTYPE((mode), __S_IFCHR)
  #define	S_ISBLK(mode)	 __S_ISTYPE((mode), __S_IFBLK)
  #define	S_ISREG(mode)	 __S_ISTYPE((mode), __S_IFREG)
  #define S_ISFIFO(mode)	 __S_ISTYPE((mode), __S_IFIFO)
  #define S_ISLNK(mode)	 __S_ISTYPE((mode), __S_IFLNK)
  #define S_ISSOCK(mode)   __S_ISTYPE((mode), __S_IFSOCK)
  ```
  > 获取到文件属性存入sbuf后，可以使用宏来判定其类型，如`S_ISDIR(sbuf->st_mode)`判断它是否是目录文件，或者用`sbuf->st_mode & S_IFMT`，看其结果是哪一个（`S_IFDIR`、`S_IFCHR`、`S_IFBLK`、`S_IFREG`、`S_IFIFO`、`S_IFLINK`、`S_IFSOCK`）。
* 关于文件的时间信息  
  在`struct stat`中有三个表示时间的字段`st_atim`、`st_mtim`、`st_ctim`
  ```c
  struct timespec st_atim;  /* Time of last access */
  struct timespec st_mtim;  /* Time of last modification */
  struct timespec st_ctim;  /* Time of last status change */
  #define st_atime st_atim.tv_sec      /* Backward compatibility */
  #define st_mtime st_mtim.tv_sec
  #define st_ctime st_ctim.tv_sec
  ```
  > 为了兼容性，又分别定义了`st_atime`、`st_mtime`、`st_ctime`，这三个是对应的`struct timespec`中的`tv_sec`段，是`time_t`类型的值，表示以秒为单位的国际时间。   
  
  可以用`utime()`函数来修改文件的最后访问时间（`Time of last acess`）和最后修改时间（`Time of last modification`），最后状态改变时间不能修改，这个是由系统维护的。
  ```c
  #include <utime.h>
  int utime (const char *pathname, const struct utimbuf *file_times)
  ```
  `struct utimebuf`定义如下：
  ```c
  struct utimbuf
  {
    time_t actime;		/* Access time.  */
    time_t modtime;		/* Modification time.  */
  };
  ```
  如果传入的`file_times`指针为空，则把文件的`st_atime`和`st_mtime`修改为系统当前时间，且进程必须拥有该文件的写权限。如果`file_times`指针不为空，则把文件的`actime`和`modtime`修改为它指定的时间，此时进程的用户`ID`必须是文件的属主，或是超级进程（`root`）。
* 文件的权限  
  每个文件有三组权限，分别对应属主、属主所在的组和其他人，权限包括读、写和运行。  
  * `access()`函数，测试进程是否对文件有相关权限很重要，可以避免无权限操作而出错，这也是逻辑上的一种严谨。测试通过，`access()`则返回`0`，不通过则返回`-1`。  
    ```c
    #include <unistd.h>
    int access(const char *pathname, int mode)
    ```
    > mode的取值可以是以下四种之一：
    > * `R_OK`      检验调用进程是否有读权限
    > * `W_OK`      检验调用进程是否有写权限 
    > * `X_OK`      检验调用进程是否有执行权限
    > * `F_OK`      检验指定的文件是否存在    
  * `umask()`函数，用来设置文件创建的权限屏蔽字
    ```c
    #include <sys/stat.h>
    mode_t umask(mode_t cmask)
    ```
    > `cmask`的每一位意义与`st_mode`是一样的，不同的是，当它的某一位设置为`0`时，表示`open()`、`create()`函数建立的文件可以设置相应的权限，为`1`时，表示不可以拥有相应的权限。比如，`cmask`为`007`（即其他人的读写执行权限位为`1`）时，新建的文件则不能设置其他人的读写执行权限。  
    函数返回修改之前的权限屏蔽字。
  * `chmod()`和`fchmode()`函数，用来改变文件的权限
    ```c
    #include <sys/stat.h>
    int chmode(const char *pathname, mode_t mode);
    int fchmode(int fd, mode_mode);
    ```
    >






# 学习中写的代码
## 时间相关
```c
#include <time.h>
#include <stdio.h>
#include <string.h>

int main(int argc, char const *argv[])
{
    time_t t, t1;
    void * ptr;
    t = time(&t1);
    printf("time() is return %ld.\nand time(&t1) t1 is %ld.\n", t, t1);
    struct tm gmtTime, localTime;
    gmtime_r(&t1, &gmtTime);
    localtime_r(&t1, &localTime);

    printf("The Green time is: %s", asctime(&gmtTime));
    printf("The local time is: %4d-%02d-%02d %02d:%02d:%02d\n", 
        localTime.tm_year + 1900, localTime.tm_mon + 1, localTime.tm_mday, localTime.tm_hour, localTime.tm_min, localTime.tm_sec);
    printf("Timezone: %d\n", gmtTime.tm_hour - localTime.tm_hour);
    return 0;
}
```
> 三种时间：
> * 以秒的形式体现的国际时间，通过`time()`函数获取
> * 以`struct tm`结构体保存的时间，从以秒时间转换而来，有两个函数：`gmtime()`把国际秒时间转换为`GMT`，`localtime()`把国际秒时间转换为`LOCAL`时间（也就是考虑时区问题）
> * 以字符串形式表示的时间，即`asctime()`根据`struct tm`生成的字符串，格式固定，带`"\n"`，如果要自定义的格式，可以使用`sprintf()`根据`struct tm`生成。
## 文件相关
每分钟生成一个"yyyyMMddHHmmss"格式文件名的文件，每秒向其中写入字符串形式的时间
```c
#include <time.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

int main(int argc, char const *argv[])
{
    time_t t;
    struct tm oldLocalTime, newLocalTime;
    time(&t);
    localtime_r(&t,&oldLocalTime);
    char filename[15] = {0};
    sprintf(filename,"%4d%02d%02d%02d%02d%02d", 
        oldLocalTime.tm_year + 1900, oldLocalTime.tm_mon + 1, oldLocalTime.tm_mday, oldLocalTime.tm_hour, oldLocalTime.tm_min, oldLocalTime.tm_sec);
    int fd;
    fd = open(filename, O_WRONLY|O_CREAT|O_APPEND,0660);
    
    while(1){
        sleep(1);
        time(&t);
        localtime_r(&t,&newLocalTime);
        if(newLocalTime.tm_sec == 0){
            close(fd);
            sprintf(filename,"%4d%02d%02d%02d%02d%02d", 
                newLocalTime.tm_year + 1900, newLocalTime.tm_mon + 1, newLocalTime.tm_mday, newLocalTime.tm_hour, newLocalTime.tm_min, newLocalTime.tm_sec);
            fd = open(filename, O_WRONLY|O_CREAT|O_APPEND,0660);
            oldLocalTime = newLocalTime;
        }
        char buf[30] = {0};
        asctime_r(&newLocalTime, buf);
        write(fd, buf, strlen(buf));
    }
    close(fd);
    return 0;
}
```
## 目录相关
打印出指定目录下的各类文件
```c
#include <dirent.h>
#include <stdio.h>

char * type[] = {"UNKNOW", "FIFO", "CHR", "", 
                "DIR", "", "BLK", "",
                "REG", "", "LINK","","SOCK"};

int main(int argc, char * argv[]){
    DIR * dp;
    struct dirent * direntiy;
    dp = opendir(argv[1]);
    printf("inode\t\treclen\ttype\tname\n");
    while((direntiy = readdir(dp)) != NULL){
        printf("%ld\t%d\t%-7.7s\t%-20.20s\n", direntiy->d_ino, 
            direntiy->d_reclen, type[direntiy->d_type],direntiy->d_name);
    }
    closedir(dp);
}
```
## 文件属性相关
以递归的方式，打印出指定目录下的文件相关属性，包括子目录下。
```c
#include <sys/stat.h>
#include <unistd.h>
#include <dirent.h>
#include <stdio.h>
#include <string.h>
#include <time.h>

char * type[] = {"UNKNOW", "FIFO", "CHR", "", 
                "DIR", "", "BLK", "",
                "REG", "", "LINK","","SOCK"};

void processDir(const char * fullPath, int level){
    for(int i = 0; i < level; i++) printf("\t");
    printf("------Start process %s-----------\n", fullPath);
    char fullPathName[4096] = {0};
    DIR * pDir;
    struct dirent *dr;
    strncpy(fullPathName, fullPath, strlen(fullPath));
    pDir = opendir(fullPathName);
    strcat(fullPathName, "/");
    while ((dr = readdir(pDir)) != NULL){
        char fullFileName[4096] = {0};
        strncpy(fullFileName, fullPathName, strlen(fullPathName));
        strcat(fullFileName, dr->d_name);
        if ( (dr->d_type == 4) && (strncmp(".", dr->d_name, strlen(dr->d_name)) != 0) && (strncmp("..", dr->d_name, strlen(dr->d_name)) != 0)){
            processDir(fullFileName, level + 1);
        }
        else{
            char out[4096] = {0};
            struct stat fstat;
            stat(fullFileName, &fstat);
            struct tm localtm;
            localtime_r(&fstat.st_ctime, &localtm);
            sprintf(out, "Type:%-5.4s iNode:%ld Size:%-10ld Last Change:%04d%02d%02d %02d:%02d:%02d Mode:%0o Name:%s\n", type[dr->d_type], fstat.st_ino, fstat.st_size,
                    localtm.tm_year + 1990, localtm.tm_mon + 1, localtm.tm_mday, localtm.tm_hour, localtm.tm_min, localtm.tm_sec, 
                    fstat.st_mode, fullFileName);
            for (int i = 0; i < level; i++) printf("\t");
            printf("%s", out);
        }
    }
}

int main(int argc, char * argv[]){
    char pathName[4096] = {0};
    chdir(argv[1]);
    getcwd(pathName, 4096);
    processDir(pathName, 0);
    return 0;
}
```
> 递归果然不是很靠谱，如果目录结构比较复杂，会出错，堆栈崩了：(。  
> 又对源码查了几遍，出错的问题不是递归引起，而是少了一句`closedir()`，在`while`循环之后，也就是`400`行之后，插入一行`closedir(pDir);`。至此，错误排除。再次提醒自己，有`open`、就有`close`，有`malloc`，就有`free`。