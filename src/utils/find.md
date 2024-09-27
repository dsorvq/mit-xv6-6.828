# find

```c
void find(char* path, char* target) {
  int fd;
  char buf[256], *p;
  struct stat st;
  struct dirent de;

  if ((fd = open(path, O_RDONLY)) < 0) {
    fprintf(2, "find: cannot open %s\n", path);
    return;
  }

  if (fstat(fd, &st) < 0) {
    fprintf(2, "find: cannot stat %s\n", path);
    close(fd);
    return;
  }

  if (st.type != T_DIR) {
    fprintf(2, "find: %s is not a dir\n", path);
    close(fd);
    return ;
  }

  if(strlen(path) + 1 + DIRSIZ + 1 > sizeof buf){
    printf("find: path too long\n");
    return ;
  }

  strcpy(buf, path);
  p = buf+strlen(buf);
  *p++ = '/';
  while (read(fd, &de, sizeof(de)) == sizeof(de)) {
    if(de.inum == 0)
      continue;
    memmove(p, de.name, DIRSIZ);
    p[DIRSIZ] = 0;
    if(stat(buf, &st) < 0){
      printf("ls: cannot stat %s\n", buf);
      continue;
    }
    if (st.type == T_DIR) {
      if (strcmp(de.name, ".") != 0 && strcmp(de.name, "..") != 0) {
        find(buf, target);
      }
    } else {
      if (strcmp(de.name, target) == 0) {
        printf("%s/%s\n", path, de.name);
      }
    }
  }

  close(fd);
}
```

```c
int main(int argc, char* argv[]) {
  if (argc != 3) {
    fprintf(2, "usage: find path_to_dir target\n");
    exit(1);
  }
  find(argv[1], argv[2]);

  exit(0);
}
```