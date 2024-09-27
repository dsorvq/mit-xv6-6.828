# pingpong

```c
int main(int argc, char *argv[]) {
  int p[2];
  char c = 'c';

  if (pipe(p) < 0) {
    panic("pong");
  }

  if (fork() == 0) {
    read(p[0], &c, 1);
    printf("%d: received ping\n", getpid());
    close(p[0]);
    write(p[1], &c, 1);
    close(p[1]);
  } else {
    write(p[1], &c, 1);
    close(p[1]);
    wait(0); // so parent won't 'read itself'
    read(p[0], &c, 1);
    printf("%d: received pong\n", getpid());
    close(p[0]);
  }

  exit(0);
}
```