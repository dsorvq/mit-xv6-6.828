# primes

```c
int main() {
  int fds[2];

  pipe(fds);

  if (fork() == 0) {
    close(fds[1]);
    close(0);
    dup(fds[0]);
    close(fds[0]);
    primes();
  } else {
    close(fds[0]);
    close(2);
    dup(fds[1]);
    close(fds[1]);
    for (int i = 2; i < 280; ++i) {
      write_int(2, i);
    }
    close(2);
    wait(0);
  }

  return 0;
}
```

```c
int read_int(int fd, int* n) {
  if (read(fd, n, sizeof(int)) != sizeof(int)) {
    return 0;
  }
  return 1;
}

void write_int(int fd, int n) {
  write(fd, &n, sizeof(int));
}
```

```c
void primes() {
  // 0 - for reading from the left
  // 1 - output primes to the console
  // 2 - for writing to the right

  int fds[2];
  pipe(fds);
  int prime, num;

  if (read_int(0, &prime) < 1) {
    return ;
  }
  printf("prime %d\n", prime);

  if (fork() == 0) {
    close(fds[1]);
    close(0);
    dup(fds[0]);
    close(fds[0]);
    primes();
  } else {
    close(2);
    close(fds[0]);
    dup(fds[1]);
    close(fds[1]);

    while (read_int(0, &num) > 0) {
      if (num % prime != 0) {
        write_int(2, num);
      }
    }
    close(2);
    wait(0);
  }
}
```