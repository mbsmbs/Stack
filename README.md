# Stack

### 스택 구현
####헤더파일
```C
// int형 스택 (헤더)

#ifndef ___IntStack
#define ___IntStack

typedef struct // 스택을 구현하는 구조체
{
    int max;    // 스택 용량 
    int ptr;    // 스택에 쌓여 있는 데이터의 개수를 나타내는 멤버  즉 배열의 인덱스를 나타낸다
    int* stk;   // 스택으로 사용할 배열을 가리키는 포인터   가장 먼저 푸시된 데이터를 저장하는 곳은 std[0] 입니다.
} IntStack;

int Initialize(IntStack *s, int max);   // 스택 초기화   배열의 메모리 공간 할당

int Push(IntStack *s, int x);           // 스택에 데이터 푸시

int Pop(IntStack *s, int *x);           // 스택에서 데이터를 팝

int Peek(const IntStack *s);            // 스택에서 데이터를 피크

int Clear(const IntStack *s);           // 스택 비우기

int Capacity(const IntStack *s);        // 스택의 최대 용량

int Size(const IntStack *s);            // 스택의 데이터 개수

int IsEmpty(const IntStack *s);         // 스택이 비어 있나요?

int IsFull(const IntStack *s);          // 스택이 가득 찼나요?

int Search(const IntStack *s);          // 스택에서 검색

void Print(const IntStack *s);          // 모든 데이터 출력

void Terminate(const IntStack *s);      // 스택 종료

#endif
```


#### C파일
```C
#include <stdio.h>
#include <stdlib.h>
#include "Stack1.h" //구조체를 정의한 헤더파일 include

/*---스택의 초기화---*/
int Initialize(IntStack* s, int max) // 구조체 포인터 변수와 스택으로 사용할 배열의 요소개수를 입력받는다.
{
    s->ptr = 0;     // 구조체 포인터가 가리키는 구조체의 멤버 변수 ptr에 접근하여 값을 0으로 초기화 한다.
    if((s->stk = calloc(max, sizeof(int))) == NULL)     // 구조체 멤버 포인터 변수에 메모리 공간 할당이 안되었을 경우
    {
        s->max = 0;     // 구조체 멤버 요소개수를 가리키는 멤버변수 max에 0으로 초기화 한다. 왜? 배열을 위한 메모리 공간을 만들때 스택은 비어있어야 한다.
        return -1;
    }

    s->max = max;       // 매개변수로 입력받은 max 요소개수 값을 구조체 멤버 요소개수 변수에 대입한다. max 요소개수의 배열(스택)이 생성된다
    return 0;
}

/*---스택에 데이터를 푸시---*/
int Push(IntStack* s, int x)
{
    if(s->ptr >= s->max)        // 스택이 가득 참
        return -1;
    s->stk[s->ptr++] = x;
    return 0;
}

/*---스택에서 데이터를 팝---*/
int Pop(IntStack* s, int* x)
{
    if(s->ptr <= 0)             // 스택이 비어있음
        return -1;
    *x = s->stk[s->ptr--];
    return 0;
}

/*---스택에서 데이터를 피크---*/
int Peek(const IntStack* s, int* x)
{
    if(s->ptr <= 0)             // 스택이 비어있음
        return -1;
    *x = s->stk[s->ptr - 1];
    return 0;
}

/*---스택 비우기---*/
void Clear(IntStack* s)
{
    s->ptr = 0;
}

/*---스택 용량---*/
int Capacity(IntStack* s)
{
    return s->max;
}

/*---스택에 쌓여있는 데이터 수---*/
int Size(IntStack* s)
{
    return s->ptr;
}

/*---스택이 비어있는가?---*/
int IsEmpty(const IntStack* s)
{
    return s->ptr <= 0;
}

/*---스택이 가득 찼는가?---*/
int IsFull(const IntStack* s)
{
    return s->ptr >= s->max;
}

/*---스택에서 검색---*/
int Search(const IntStack* s, int x)
{
    int i;
    for( i = s->ptr - 1; i >= 0; i++)   // 꼭대기에서 바닥까지 선형 검색
    {
        if(s->stk[i] == x)
            return i;                   // 검색 성공
        return -1;                      // 검색 실패
    }
}

/*---모든 데이터 출력---*/
void Print(const IntStack* s)
{
    int i;
    for(i = 0; i < s->ptr; i++)         // 배열의 모든 요소를 출력
    {
        printf("%d", s->stk[i]);
    }
    putchar("\n");
}

/*---스택 종료---*/
void Terminate(IntStack* s)
{
    if(s->stk != NULL)
    {
        free(s->stk);                   // 배열 삭제
    }
    s->max = s->ptr = 0;
}



int main(void)
{
    IntStack s; // 구조체 선언

    if(Initialize(&s, 64) == -1)    //초기화 함수를 실행 했는데 -1을 반환하면
    {
        puts("스택 생성에 실패하였습니다.");
        return 1;
    }

    while(1)
    {
        int menu, x;    // 변수 선언
        
        printf("현재 데이터 수 : %d / %d\n", Size(&s), Capacity(&s));   // 스택에 쌓여있는 수와 스택의 용량을 출력
        printf("(1)푸시 (2)팝 (3)피크 (4)출력 (0)종료 : ");             
        scanf("%d", &menu);     // 입력받은 값을 menu변수에 대입한다.

        if(menu == 0) break;    // menu변수에 아무 값이 없으면 종료

        switch (menu)       //menu 변수에 값이 있으면
        {
            case 1:     // 1을 입력하면 푸시
                printf("데이터 : ");
                scanf("%d", &x);        // 데이터를 입력 받아 x에 대입한다
                if(Push(&s, x) == -1)   // 데이터 입력에 실패하면
                    puts("\a오류 : 푸시에 실패하였습니다.");
                break;

            case 2:     // 2를 입력하면 팝
                if(Pop(&s, &x) == -1)                       // 팝 실패시       
                    puts("\a오류 : 팝에 실패하였습니다.");
                else
                    printf("팝 데이터는 %d입니다.\n", x);    // 팝 성공시
                break;

            case 3:     // 3을 입력하면 피크
                if(Peek(&s, &x) == -1)                      // 피크 실패
                    puts("\a오류 : 피크에 실패하였습니다.");
                else
                    printf("피크 데이터는 &d입니다.", x);    // 피크 성공
                break;
        
            case 4:     // 4를 입력하면 출력
                Print(&s);   //스택 배열에 존재하는 모든 데이터 출력
                break;
        }
    }

    Terminate(&s);      // 스택 종료
    return 0;
}
```
