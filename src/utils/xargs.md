# xargs

```c
int main(int argc, char* argv[]) {
  char* arguments[MAXARG];

  if (argc < 2) {
    printf("usage: xargs prog [params]\n");
    exit(0);
  }

  // -1 xargs, +1 for arguments_line, +1 for 0 at the end
  int arguments_size = argc + 1;
  if (arguments_size > MAXARG) {
    printf("too many arguments\n");
    exit(0);
  }

  for (int i = 1; i < argc; ++i) {
    if (strlen(argv[i]) + 1 > 512) {
      printf("argument is too large\n");
      exit(0);
    }
    arguments[i - 1] = argv[i];
  }
  arguments[arguments_size - 1] = 0; // null terminate arugments

  xargs(arguments, arguments_size);

  exit(0);
}
```

```c
void xargs(char** arguments, int arguments_size) {
  char line[512];
  while (getline(line) > 0) {
    arguments[arguments_size - 2] = line;

    if (fork() == 0) {
      exec(arguments[0], arguments);
      printf("cannot open %s\n", arguments[0]);
      exit(1);
    } else {
      wait(0);
    }
  }
}
```

```c
int getline(char* line) {
  char c;
  int i = 0;
  while (read(0, &c, 1) == 1) {
    if (c == '\n') {
      break;
    }
    line[i++] = c;
  }
  line[i] = '\0';
  return i;
}
```