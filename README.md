#include <iostream>
#include <stdlib.h>
#include <time.h>
#include <conio.h>
#include <windows.h>

using namespace std;

class Pro_Man {   // 맵 생성 부모 클래스
protected:		  // 자식 클래스에 상속 가능
	int imap[12][12];  // 전체 맵 변수
public:
	void Draw_Map();  // 맵 생성
};

void Pro_Man::Draw_Map()
{
	int a, b, c;
	int rd;

	srand(time(NULL));
	c = 0;
	rd = rand() % 3; // 랜덤으로 벽돌 모양 지정

	for (a = 0; a < 12; a++) {
		for (b = 0; b < 12; b++) {
			if (a <= 2) {   // 벽돌 모양 지정
				if (rd == 0) imap[a][b] = 1;
				if (rd == 1) imap[a][b] = 2;
				if (rd == 2) imap[a][b] = 3;
				++c;
			}
			else imap[a][b] = 0;  // 나머지는 빈 공간
			if (c == 3) {
				c = 0;   // 벽돌 크기 지정
				rd = rand() % 3; // 랜덤으로 벽돌 모양 지정
			}
		}
	}
}

class Ball : public Pro_Man {  // 공 조정 자식 클래스 (부모 클래스 상속)
private:
	int dr;				// 여러가지 옵션 변수
	int ballx, bally;   // 볼의 좌표
	int map[12][12];    // 맵 정보
public:
	void Init_G();		// 초기화 및 전체 프로그램 조정
	void Move_Ball();	// 공 쏘기
	void Get_Map();		// 부모 클래스에서 생성된 맵을 받음
	int Get_Val(int i, int j);
	void Key_In();		// 공 쏘기 위한 키 선택

	void Move_Up();  // 수직으로 상승
	void Move_Right();  // 오른쪽으로 상승
	void Move_Left();  // 왼쪽으로 상승

	void Move_Down(); // 하강 조정
	void Get_Down(); // 수직으로 하강
	void Right_Down();//오른쪽으로 하강
	void Left_Down(); // 왼쪽으로 하강

	void Print_Ball(); // 맵출력
};

void Ball::Init_G()
{
	Draw_Map();  // 화면 출력

	Move_Ball(); // 공 조정
}

void Ball::Move_Ball()
{
	int a;

	Get_Map();   // 맵 획득
	bally = 11;
	dr = rand() % 12;  // 최초 x 좌표 설정
	ballx = dr;
	map[bally][ballx] = 4;  // 최초 공 설정

	Print_Ball();  // 처음 화면
	Key_In();      // 게임 시작
}

void Ball::Get_Map() // 부모클래스의 맵 정보 받음
{
	int i, j;

	for (i = 0; i < 12; i++) {
		for (j = 0; j < 12; j++) {
			map[i][j] = Get_Val(i, j);
		}
	}
}

int Ball::Get_Val(int i, int j)  // 부모클래스의 맵 정보 받음
{
	return imap[i][j]; // 부모클래스의 배열 imap의 정보를 받음
}

void Ball::Key_In()  // 키 입력
{
	while(TRUE){  // 무한 루프
			dr = rand() % 3;
			if (dr == 0) Move_Up();
			if (dr == 1) Move_Right();
			if (dr == 2) Move_Left();
	}
}

void Ball::Move_Up()  // 수직 상승
{
	int n, m;  // 공의 좌표 임시저장 공간
	int cnt;

	cnt = bally;  // 높이 y 좌표 설정
	while (cnt > 3) {
		system("cls"); // 화면 갱신

		m = ballx;  // 이전 좌표 확보
		n = bally;
		map[n][m] = 0;  // 이전 좌표 공을 무효화
		--bally;    // 공의 수직 상승
		map[bally][ballx] = 4;

		Print_Ball();  // 공표시
		Sleep(30);
		--cnt;
	}
	Move_Down();
}

void Ball::Move_Right()  // 우측 상승
{
	int n, m;
	int rd, cnt;
	int tg;

	cnt = bally;    // y 좌표 설정
	while (cnt > 3) {
		system("cls");  // 화면 갱신

		m = ballx;   // 이전 좌표 확보
		n = bally;
		map[n][m] = 0;  // 이전 공 삭제
		--bally;    // 상승

		if (ballx < 11) ballx++;  // x 좌표 범위 안에서 증가
		if (ballx == 11) cnt = 2;  // x 좌표 초과 시 증가 멈춤

		map[bally][ballx] = 4;  // 공표시

		Print_Ball();  // 출력
		Sleep(30);
		--cnt;
	}

	if (bally > 3) Move_Left();  // y 좌표가 아직 남아있을 때, (오른쪽) x 좌표가 벽에 부딪히면 (좌측으로) 공방향 전환
	Move_Down();  // 하강
}

void Ball::Move_Left()
{
	int n, m;
	int rd, cnt;
	int tg;

	cnt = bally;
	while (cnt > 3) {
		system("cls");

		m = ballx;
		n = bally;
		map[n][m] = 0;
		--bally;    // 상승

		if (ballx > 0) ballx--;
		if (ballx == 0) cnt = 2;

		map[bally][ballx] = 4;

		Print_Ball();
		Sleep(30);
		--cnt;
	}

	if (bally > 3) Move_Right(); // y 좌표가 아직 남아있을 때, (왼쪽) x 좌표가 벽에 부딪히면 (우측으로) 공방향 전환
	Move_Down();
}

void Ball::Move_Down()
{
	int nm;

	nm = rand() % 3;

	if (nm == 0) Get_Down();  // 수짓 하강
	if (nm == 1) Right_Down(); // 우측 하강
	if (nm == 2) Left_Down();  // 좌측 하강
}

void Ball::Get_Down()  // 수직 하강
{
	int n, m;
	int cnt;

	cnt = bally;
	while (cnt <= 10) {
		system("cls");

		m = ballx;
		n = bally;
		map[n][m] = 0;
		++bally;   // 수직 하강
		map[bally][ballx] = 4;

		Print_Ball();
		Sleep(30);
		++cnt;
	}
}

void Ball::Right_Down()
{
	int n, m;
	int rd, cnt;
	int tg;

	cnt = bally;
	while (cnt <= 10) {
		system("cls");

		m = ballx;
		n = bally;
		map[n][m] = 0;
		++bally;    // 하강

		if (ballx < 11) ballx++;  // x 좌표 우측조정, 우측으로 하강
		if (ballx == 11) cnt = 11; // 벽에 부딪혔는지 확인

		map[bally][ballx] = 4;

		Print_Ball();
		Sleep(30);
		++cnt;
	}

	if (bally < 10) Left_Down(); // y 좌표가 아직 남아있을 때, (오른쪽) x 좌표가 벽에 부딪히면 (좌측으로) 공방향 전환
}

void Ball::Left_Down()
{
	int n, m;
	int rd, cnt;
	int tg;

	cnt = bally;
	while (cnt <= 10) {
		system("cls");

		m = ballx;
		n = bally;
		map[n][m] = 0;
		++bally;    // 하강

		if (ballx > 0) ballx--;  // x 좌표 좌측조정,좌측으로 하강
		if (ballx == 0) cnt = 11;

		map[bally][ballx] = 4;

		Print_Ball();
		Sleep(30);
		++cnt;
	}

	if (bally < 10) Right_Down(); // y 좌표가 아직 남아있을 때, (왼쪽) x 좌표가 벽에 부딪히면 (우측으로) 공방향 전환
}

void Ball::Print_Ball() // 화면 출력
{
	int a, b;

	for (a = 0; a < 12; a++) {
		for (b = 0; b < 12; b++) {
			if (map[a][b] == 0) cout << "  ";  //두 칸
			if (map[a][b] == 1) cout << "■";  //■
			if (map[a][b] == 2) cout << "◆";  //◆
			if (map[a][b] == 3) cout << "☆";  //☆
			if (map[a][b] == 4) cout << "●";  //●
		}
		cout << endl;;
	}
}

int main()
{
	Ball bloc;

	bloc.Init_G();

	return 0;
}
