# sleep

```c
int main(int argc, char *argv[]) {
  int seconds;

  if(argc <= 1) {
    fprintf(2, "sleep: missing operand\n");
    exit(1);
  }

  seconds = atoi(argv[1]);
  sleep(seconds);

  exit(0);
}
```