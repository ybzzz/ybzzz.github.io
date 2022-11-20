---
title: Select v.s. Poll v.s. Epoll
date: 2022-11-20 15:36:32
categories:
- Software Development
- Operating System
- Linux
tags:
- Linux
---

Describe how select/poll/epoll work and the difference.

<!--more-->


## Overview
In Linux system, everything is a file. Typical system that works with many IO source need to handle the IO resource.

### IO Multiplexing
A kernel mechanism for polling over a set of file description.
There are 3 options in Linux:
+ select (man 2 select)
+ poll (man 2 poll)
+ epoll (man 7 poll)

## select
Pseudo-Code
```
// return true is fd is ready for I/O
bool is_ready(int fd);

struct fd_info{
	int fd;
	bool ready;
};

int select(set<fd_into> fds, int max_fd) {
	int ready_cnt = 0;
	while (ready_cnt == 0) {
		for (int i = 0;i < max_fd; i++){
			if (is_ready(i)){
			auto it = fds.find(i);
			ready_cnt;
			}
		}
	}
	return ready_cnt;
}
```

Explanation:
+ The caller passes a set of fds they want to monitor and the maximum fd (actually max fd + 1) among all interesting fds.
+ A caller has to reset the fd set per select call. That means, if select is called in a loop, then we need to reset the fd set in each iteration.
+ The complexity of the inner loop is O(max_fd + 1). When the fds are sparse, there will be a lot of waste. For example, if the fds are {1, 10, 1023}, then the loop size is 1024 instead of 3.

Example Usage:
```
set<fd_info> fds;
while (1) {
  // Note that we need to re-initialize fds in each loop.
  fds.clear();
  fds.inert({.fd = 1})
  fds.inert({.fd = 100})

  int ready_cnt = select(fds, /*max_fd=*/100 + 1);
  assert(ready_cnt > 0);
  for (int i = 0; i < fds.size(); i++) {
    if (fds[i].ready) {
      // Use fds[i].fd
    }
  }
}
```

## poll

Pseudo-Code
```
// Returns true if fd is ready for I/O.
bool is_ready(int fd);

struct fd_info {
  int fd;
  bool ready;
};

int poll(struct fd_info* fds, int nfds) {
  int ready_cnt = 0;
  while(ready_cnt == 0) {
    for (int i = 0; i < nfds; i++) {
      if (is_ready(fds[i])) {
        fds[i].ready = true;
        ready_cnt++;
      } else {
        fds[i] = false;
      }
    }
  }
  return ready_cnt;
}
```

Explanation:
+ Unlike select, a caller no longer needs to reset the fds per call because poll will reset the ready flag of any unready fds.
+ The complexity of the inner loop is O(n) where n is the number of fds to monitor. If the fds are {1, 10, 1023}, then the complexity is O(3).
+ In Linux code, both select and poll implementation are in fs/select.c file because they both use the same underlying kernel poll functions.

Example Usage:
```
// Only need to initialize fds once.
fd_info fds[2];
fds[0].fd = 1;
fds[1].fd = 100;

int nfds = 2;

while (1) {
  int ready_cnt = poll(fds, nfds);
  assert(ready_cnt > 0);
  for (int i = 0; i < nfds; i++) {
    if (fds[i].ready) {
      // Use fds[i].fd
    }
  }
}
```

## epoll
Let’s assume we have an API `void add_monitor(const vector<int>& all_fds, vector<int>& ready_fds)` which triggers an external thread to constantly monitor all_fds and add ready fds in it to ready_fds. 

Pseudo-Code
```
// Start monitoring fds in `all_fds` and constantly adds ready ones to
// `ready_fds`.
void add_monitor(const vector<int>& all_fds, vector<int>& ready_fds);

struct fd_info {
  int fd;
  bool ready;
};

struct epoll_info {
  vector<int> all_fds;
  vector<int> ready_fds;
};

map<int, epoll_info> epoll_info_by_epoll_id;

// Create an epoll instance and return its id.
int epoll_create() {
  return epoll_info_by_epoll_fd.size();
}

// Add a fd to monitor to the epoll instance.
void epoll_add(int epoll_id, int fd) {
  epoll_info_by_epoll_id[epoll_id].push_back(fd);
}

// Wait until at least one fd is ready. Return number of ready fds.
// Afte the function returns, the first `ready_cnt` of `ready_fds` contain
// ready fds. The rest can be ignored.
int epoll_wait(int epoll_id, struct fd_info* ready_fds) {
  int ready_cnt = 0;

  struct epoll_info info = epoll_info_by_epoll_id[epoll_id];
  add_monitor(info.allfds, info.ready_fds);
  while (ready_cnt == 0) {
    ready_cnt = ready_fds.size();
    for (int i = 0; i < ready_cnt; i++) {
      ready_fds[i].fd = ready_fds[i];
      ready_fds[i].ready = true;
    }
  }
  return ready_cnt;
}
```

Explanation:
+ Unlike select and poll both of which only provide one API, epoll is not a single API but a group of 3 APIs.
+ epoll_create and epoll_add are called to set up the epoll instance while epoll_wait can be called in a loop to constantly wait on the fds added by epoll_add.
+ The complexity of the inner loop is O(ready fds). The worst case complexity is still O(n) like poll. However, in the case that the ready fds are mostly much less than fds to monitor, epoll has better performance than poll. In other words, even when two algorithms both have complexity O(n), in reality, n=3 and n=10000 may matter a lot.

Example Usage:
```
int epoll_id = epoll_create();
epoll_add(epoll_id, 1);
epoll_add(epoll_id, 100);

while (1) {
  struct fd_info fds[2];
  int ready_cnt = epoll_wait(epoll_id, fds);
  assert(ready_cnt > 0);
  for (int i = 0; i < ready_cnt; i++) {
    // Use fds[i].fd
  }
}
```

## Performance
+ when we only need to monitor few fds, and most of them are active, better to use select and poll
+ when need to monitor lots of fds, and only few of them are active at the same time, better to use epoll

## Reference
>https://hechao.li/2022/01/04/select-vs-poll-vs-epoll/
https://eklitzke.org/blocking-io-nonblocking-io-and-epoll
