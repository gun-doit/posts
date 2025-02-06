> **[SSD ( Solid State Drive )](https://runmad84.com/ssd%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80/)**

# SSD란?

기존 HDD의 약점을 보완하고 향상된 기능을 가진 저장 장치를 의미한다.

### 특징
- HDD는 기계적인 부품들로 작동하는 데 반해 SDD는 플래시메모리 기능을 사용함

- 집적회로어셈블리를 사용하여 데이터를 저장하는 저장 장치 플래시 메모리 기술을 활용하여 엑세스 시간을 단축하고 전반적인 성능을 향상시킨다.

- 리드와 같은 패턴으로 구성된 NAND 플래시 메모리 칩으로 구성되어 있음. 이러한 메모리 칩에는 전원이 꺼진 경우에도 데이터를 저장하고 유지할 수 있는 수십억 개의 개별 트랜지스터가 포함되어 있다. 데이터는 전기적으로 프로그래밍하고 삭제할 수 있는 메모리 셀에 저장된다. 이는 비휘발성 저장을 허용하므로 전원 공급 장치가 분리된 경우에도 데이터가 그래도 유지된다.

### 내구성과 안전성

→ 기계적 구성 요소가 없기 때문에 충격이나 진동으로 인한 물리적 손상이 거의 없다


### 동작 원리
인터페이스를 통해 데이터를 저장하고자 하는 경우 컨트롤러는 플래시 메모리의 어느 곳에 데이터를 저장할지를 결정하고 플래시 메모리의 물리적인 주소를 지정한 후 데이터를 저장하게 된다. **채널 또는 버스**라고 하는 것을 몇 개로 나누어서 저장하느냐에 따라 SSD의 성능이 달라진다.  

1. **읽기 동작**
    - 하나의 페이지보다 작은 크기의 데이터를 읽을 수는 없다. 사용자가 단 하나의 바이트만 읽기를 요청한다면 실제 SSD는 하나의 페이지를 통째로 읽은 다음 불 필요한 데이터는 모두 버리고 사용자가 요청한 한 바이트만 반환한다.
2. **쓰기 동작**
    - 페이지 단위로 하나의 페이지 또는 여러 개의 페이지로 실행된다. ( 페이지에 데이터를 쓰는 것을 “프로그램”이 라고도 한다.
    - NAND 플레시 메모리의 페이지는 반드시 free 상태에만 쓰기를 할 수 있다. 데이터가 변경되면, 페이지의 내용은 내부 레지스터로 복사된 후 레지스터에서 변경되어 새로운 free상태의 페이지로 기록되는 것이다.  
        → 이렇게 변경된 데이터가 새로운 페이지에 완전히 기록되면 페이지는 마킹되고 삭제되기 전까지 그 상태로 남게 된다.  
        
3. **삭제 동작**
    - 페이지는 덮어 쓰기가 불가능하기 때문에 한번상태로 된 페이지는 반드시 삭제하는 작업을 거쳐서 상태로 전이할 수 있다. 그러나 삭제는 단일 페이지 단위로 처리될 수 없고, 그 페이지가 포함된 블록을 통째로 삭제해야 한다. 사용자는 읽기와 쓰기 명령만 데이터 액세스를 위해 사용할 수 있으며 삭제 명령은 컨트롤러가 공간이 필요할 때 자동적으로 내부 명령을 실행해서 사용한다.

---

> # 개요
> SSD를 가상으로 구현
> Test Shell 프로그램을 제작하여 SSD 동작을 테스트 할 수 있다.

### 구현정보
- 저장할 수 있는 최소 공간의 사이즈는 4KB
- 400Byte를 저장할 수 있는 가상 SSD 구현
- LBA의 단위는 4Byte


## 가상 SSD 구현 정보
저장되는 데이터는 16진수 데이터를 ASCII코드로 변환하여 저장
읽어오는 데이터는 ASCII문자를 16진수 데이터로 읽어옴
저장영역은 txt파일로 구현

### SSD Code
```c
#include <stdio.h>
#include <stdint.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>
#define MAX_LBA 100

int32_t NAND_WRITE(int32_t LBA, int8_t *value);
void NAND_DELETE(int32_t LBA, uint8_t *buffer, size_t byte_read);
int32_t NAND_READ(int32_t LBA);
void WRITE_LOG(uint8_t *log);

void WRITE_LOG(uint8_t *log){
    time_t timer = time(NULL);
    struct tm* t;
    t = localtime(&timer);
    uint8_t log_data[200];
    sprintf((char *)log_data, "[%d-%02d-%02d %02d:%02d:%02d] %s\n", 
            t->tm_year + 1900, t->tm_mon+1, t->tm_mday, 
            t->tm_hour, t->tm_min, t->tm_sec, log);

    FILE *LOG = fopen("log.txt", "a");
    if (LOG == NULL) {
        printf("log file not found\n");
        return;
    }
    fwrite(log_data, sizeof(uint8_t), strlen((char *)log_data), LOG);
    fclose(LOG);
    return;
}

// 쓰기 동작
int32_t NAND_WRITE(int32_t LBA, int8_t *value){
    FILE *NAND = fopen("NAND.txt", "r+");

    // 파일을 열지 못함.
    if (NAND == NULL) {
        WRITE_LOG((uint8_t *)"NAND.txt not found\n");
        return -1;
    }

    // 저장될 데이터가 너무 큼.
    if (LBA >= MAX_LBA) {
        WRITE_LOG((uint8_t *)"[ERROR] Write to NAND failed: overflow\n");
        fclose(NAND); // 파일을 닫아줌
        return -1;
    }

    fseek(NAND, LBA * sizeof(int32_t), SEEK_SET);
    uint8_t buffer[100] = {0};
    size_t bytes_read = fread(buffer, sizeof(uint8_t), 4, NAND);

    if (bytes_read == 0) { 
        WRITE_LOG((uint8_t *)"[ERROR] read NAND.txt failed\n");
        fclose(NAND); // 파일을 닫아줌
        return -1;
    } else if (buffer[0] != '0') {
        NAND_DELETE(LBA, buffer, bytes_read);
    }

    // 데이터를 쓰기 전에 파일 포인터를 다시 올바른 위치로 이동
    fseek(NAND, LBA * sizeof(int32_t), SEEK_SET);

    // 파싱 및 저장
    int8_t *ptr = value + 2;
    uint8_t data[100] = {0}; // 로그를 위한 파싱된 데이터를 저장
    while (*ptr != '\0') {
        // 파싱
        int8_t temp[3];
        strncpy(temp, ptr, 2);
        temp[2] = '\0';

        // 파싱된 16진수 문자열을 data에 추가
        strncat((char *)data, temp, 2);

        int32_t x = strtol(temp, NULL, 16);
        int8_t WRITE_VALUE = (int8_t)x;

        // 저장
        fwrite(&WRITE_VALUE, sizeof(int8_t), 1, NAND);
        fflush(NAND); // 버퍼 비우기

        ptr += 2;
    }

    // 로그 작성
    uint8_t log[200];
    sprintf((char *)log, "[WRITE] LBA : %d, DATA : 0x%s", LBA, data);
    WRITE_LOG(log);

    fclose(NAND);
    return 0;
}

void NAND_DELETE(int32_t LBA, uint8_t *buffer, size_t byte_read) {
    uint8_t log[200];    
    sprintf((char *)log, "[DELETE] LBA : %d, DATA : 0x%02x%02x%02x%02x", LBA, buffer[0], buffer[1], buffer[2], buffer[3]);
    WRITE_LOG(log);
}

int32_t NAND_READ(int32_t LBA) {
    FILE *NAND = fopen("NAND.txt", "rb");

    uint8_t buffer[4];

    // 파일을 열지 못함.
    if (NAND == NULL) {
        WRITE_LOG((uint8_t *)"NAND.txt not found\n");
        return -1;
    }

    fseek(NAND, LBA * sizeof(int32_t), SEEK_SET);

    size_t bytes_read = fread(buffer, sizeof(uint8_t), 4, NAND);

    if (bytes_read == 0) {
        WRITE_LOG((uint8_t *)"[ERROR] read NAND.txt failed\n");
        fclose(NAND);
        return -1;
    }

    uint8_t log[200];    
    sprintf((char *)log, "[READ] LBA : %d, DATA : 0x%02x%02x%02x%02x", LBA, buffer[0], buffer[1], buffer[2], buffer[3]);
    printf("[READ] LBA : %d, DATA : 0x%02x%02x%02x%02x", LBA, buffer[0], buffer[1], buffer[2], buffer[3]);
    WRITE_LOG(log);

    fclose(NAND);
    return 0;
}

// SSD main
int32_t main(int32_t argc, int8_t **argv) {
    // Logical block addressing
    int32_t LBA = -1;
    if (argc >= 3 && (strcmp(argv[1], "WRITE") == 0 || strcmp(argv[1], "READ") == 0)) {
        LBA = strtol(argv[2], NULL, 10);
    }

    // 명령어 처리
    if (strcmp(argv[1], "WRITE") == 0 && argc >= 4) {
        NAND_WRITE(LBA, argv[3]);
    } else if (strcmp(argv[1], "READ") == 0) {
        NAND_READ(LBA);
    } else if (strcmp(argv[1], "FULLWRITE") == 0 && argc >= 3) {
        for (int i = 0; i < MAX_LBA; ++i) {
            NAND_WRITE(i, argv[2]);
        }
    } else if (strcmp(argv[1], "FULLREAD") == 0) {
        for (int i = 0; i < MAX_LBA; ++i) {
            NAND_READ(i);
            printf("\n");
        }
    } else if (strcmp(argv[1], "EXIT") == 0) {
        return 0;
    } else {
        printf("INVALID COMMAND\n");
        return -1;
    }

    return 0;
}
```
### Test Shell Code
```c
#include<stdio.h>
#include<stdlib.h>
#include<string.h>

void helper(){
    //명령어
    printf("=========================================== HELP ===========================================\n");
    printf("write [lba] [data]: Write data to the specified logical block address (LBA) of the device.\n");
    printf("read [lba] : Read data from the specified logical block address (LBA) of the device.\n");
    printf("fullwrite [data]: Write data to the entire device.\n");
    printf("fullread: Read data from the entire device.\n");
    printf("exit: Exit the program.\n");
    printf("============================================================================================\n");
}
int main(){
    while(1){
        char cmd[1024];
        printf("Jang >> ");
        
        fgets(cmd, sizeof(cmd), stdin);
        cmd[strlen(cmd)-1] = '\0';
        fflush(stdin);
        if(strlen(cmd) == 0) continue;

        char *token = strtok(cmd," ");
        char command[1024];
        command[0] = '\0';
        
        strcat(command, "SSD.exe ");
        while(token != NULL){
            if(strcmp(token, "write") == 0){
                strcat(command,"WRITE");
            } else if(strcmp(token, "read") == 0){
                strcat(command,"READ");
            } else if(strcmp(token, "exit") == 0){
                return 0;
            } else if(strcmp(token, "help") == 0){
                helper();
            } else if(strcmp(token, "fullwrite") == 0){
                strcat(command,"FULLWRITE");
            } else if(strcmp(token, "fullread") == 0){
                strcat(command,"FULLREAD");
            } else if(strcmp(token, "testapp1") == 0){
                system("SSD.exe FW 0x21341234");
                system("SSD.exe FR");
                break;
            } else if(strcmp(token, "testapp2") == 0){
                for(int i = 0;i<5; i++){
                    char buffer[1024];
                    buffer[0] = '\0';
                    sprintf(buffer,"SSD.exe W %d 0xAAAABBBB", i);
                    for(int j = 0; j<30; j++){
                        system(buffer);
                    }
                }
                for(int i = 0;i<5; i++){
                    char buffer[1024];
                    buffer[0] = '\0';
                    sprintf(buffer,"SSD.exe W %d 0x12345678", i);
                    system(buffer);
                }
                
                for(int i = 0;i<5; i++){
                    char *buffer;
                    sprintf(buffer,"SSD.exe R %d", i);
                    system(buffer);
                }
                break;
            }else {
                strcat(command, " ");
                strcat(command,token);
            }

            //띄어쓰기 파싱
            token = strtok(NULL, " ");
        }

    
        system(command);

        
        
    }
}
```
### Log reader Code

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <windows.h>

#define LINES_TO_READ 30
#define MAX_LINE_LENGTH 256

void tail(const char *filename, int num_lines);

int main() {
    const char *filename = "log.txt";
    while (1) {
        system("cls");  // 콘솔을 지워서 갱신된 내용을 볼 수 있도록 함
        tail(filename, LINES_TO_READ);
        Sleep(500);  // 0.5초 대기 후 다시 읽음
    }
    return 0;
}

void tail(const char *filename, int num_lines) {
    FILE *file = fopen(filename, "r");
    if (file == NULL) {
        perror("Error opening file");
        return;
    }

    char **lines = (char **)malloc(num_lines * sizeof(char *));
    for (int i = 0; i < num_lines; i++) {
        lines[i] = (char *)malloc(MAX_LINE_LENGTH * sizeof(char));
    }

    int count = 0;
    while (fgets(lines[count % num_lines], MAX_LINE_LENGTH, file) != NULL) {
        count++;
    }

    int start = count > num_lines ? count % num_lines : 0;
    int total = count < num_lines ? count : num_lines;

    for (int i = 0; i < total; i++) {
        printf("%s", lines[(start + i) % num_lines]);
    }

    for (int i = 0; i < num_lines; i++) {
        free(lines[i]);
    }
    free(lines);

    fclose(file);
}
```

## 결과
![Image](https://poisonpotato.site/public/1736405492879.png)
![Image](https://poisonpotato.site/public/1736405508290.png)
![Image](https://poisonpotato.site/public/1736405522314.png)

---

> ### 참조 링크
[1. 동작원리](https://blog.naver.com/ohmydata00/221183027428)
[2. SSD ( Solid State Drive )](https://runmad84.com/ssd%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80/)
[3. SSD란](https://dataonair.or.kr/db-tech-reference/d-lounge/expert-column/?mod=document&uid=52346)