维护了一个实际上是 bitset 的 fd_set

```cpp
int select(int nfds, 
		   fd_set *_Nullable restrict readfds,
           fd_set *_Nullable restrict writefds,
           fd_set *_Nullable restrict exceptfds,
           struct timeval *_Nullable restrict timeout);
// nfds-最大fd编号
// readfds-记录哪些fd有读事件
// writefds-记录哪些fd可写
// exceptfds-记录哪些fd有异常
// timeout-超时时间
// 修改传入的fd_set，记录哪些fd有事件

void FD_CLR(int fd, fd_set *set);	// 清空fd_set
int  FD_ISSET(int fd, fd_set *set);	// 查询fd_set某一位
void FD_SET(int fd, fd_set *set);	// 设置fd_set某一位为1
void FD_ZERO(fd_set *set);			// 设置fd_set某一位为0
```
