//컴퓨터 정보공학과 1801152 박세건 | 2학년 1학기 프로젝트(Quine-McClusky)
#include<iostream>
#include<cmath>
#include<windows.h>
#include<conio.h>
#define UP 72
#define DOWN 80
using namespace std;

int input[16];   //처음에 입력받은 값
int input_count;   //입력받은 수의 개수
int ch_input[16][5];   //[몇개 입력을 받았는지][입력받은값을 2진수로 변환한 값, 4번째칸은 십진수 값]
int group_input[5][5][100][5];  //[재묶음 횟수][1의 개수][그룹화 된 값들 순서][2진수,4번째는 10진수] 그룹화한 값들
int group_count[5][5]; //[재묶음 횟수][그룹화 된 값들의 개수]
int before_regroup[5][5][100][17];     //재묶음 되기전의 10진수 값들, 어떤 값들이 재묶음 되었는지 알기위해
int prime_implicants_check[100][5][32];		//prime_implicants가 어떤 값들인지 알기위해
int prime_implicants[2][15][20];		//prime_implicants 값, 맨앞에 2는 중복되기전은 첫번째에 중복된 값들을 없애준 후에 2에 저장했다.
int prime_count[2];		//prime_implicants 개수
int all_check;		//0~15까지의 수를 입력받았을때의 오류를 확인하기위해, 0~15까지의 모든 수를 입력받았을때 상관없는 변수들이 자꾸 값들이 바뀌는 오류가 발생한다
int graph_prime[16];		//prime implicants 값들을 크기 순서로 정렬하기 위해
int essential_pi[16];		//essential pi값들을 추출하기 위해
int final[32][4];		//논리식을 도출하기위해
int x[17]/*prime_implicants 값들의 모음*/, y[17]/*essential pi 를 구하기위한 변수*/, z[35]	/*숨겨진 Essential PI를 찾기위해 사용하는 변수*/, f_z[35]/*가로세로 제거해주기위해 사용하는 변수*/;
int key, all_print = 5;

void input_f() {   //입력받는 값
	for (int i = 0; i < 16; i++) {
		cout << ((i / 2) / 2) / 2 << ((i / 2) / 2) % 2 << (i / 2) % 2 << i % 2 << "  (" << i << ")   (사용=1,미사용=0)";
		cin >> input[i];
	}
	cout << endl;
}

void change_f() {   //입력받은 값 2진수로 변환, 10진수 값은 배열에 4번째에 저장
	if (all_print == 5) {
		for (int i = 0; i < 16; i++) {
			if (input[i] == 1) {
				ch_input[input_count][4] = i;
				ch_input[input_count][3] = i % 2;
				ch_input[input_count][2] = (i / 2) % 2;
				ch_input[input_count][1] = ((i / 2) / 2) % 2;
				ch_input[input_count][0] = ((i / 2) / 2) / 2;
				input_count++;
			}
		}
	}
	for (int i = 0; i < input_count; i++) { //2진수 출력문
		for (int j = 0; j < 4; j++) {
			cout << ch_input[i][j];
		}
		cout << "     (" << ch_input[i][4] << ")";
		cout << endl;
	}cout << endl;
}

void group_f() {    //1의 개수에 따른 그룹화
	if (all_print == 5) {
		for (int i = 0; i < input_count; i++) { //1의 개수를 세주고 그룹화 배열에 옮겨서 저장
			int count = 0;
			for (int j = 0; j < 4; j++) {
				if (ch_input[i][j] == 1) { count++; }
			}
			for (int k = 0; k < 5; k++) {
				if (count == k) {
					for (int j = 0; j < 5; j++) {
						group_input[0][k][group_count[0][k]][j] = ch_input[i][j];
					}
					group_count[0][k]++;
				}
				else if (count == k) {
					for (int j = 0; j < 5; j++) {
						group_input[0][k][group_count[0][k]][j] = ch_input[i][j];
						group_count[0][k]++;
					}
				}
			}
		}
	}
	for (int i = 0; i < 5; i++) {   //출력문
		cout << "1의 개수가 " << i << "개-------------------------" << endl;
		for (int j = 0; j < group_count[0][i]; j++) {
			for (int k = 0; k < 4; k++) {
				cout << group_input[0][i][j][k];
			}
			cout << "     (" << group_input[0][i][j][4] << ")";
			cout << endl;
		}
	}
	cout << endl;
}

void regroup_f() {  //재묶음 해주기
	if (all_print == 5) {
		for (int N = 0; N < 4; N++) {
			for (int i = 0; i < 4; i++) {   //다음 칸에있는 값들과 비교 하기때문에 4번만 반복
				for (int j = 0; j < group_count[N][i]; j++) {
					for (int k = 0; k < group_count[N][i + 1]; k++) {
						int count = 0;
						for (int l = 0; l < 4; l++) {
							if (group_input[N][i][j][l] != group_input[N][i + 1][k][l]) { count++; }
						}
						if (count == 1) {      //합쳐지는 조건
							for (int l = 0; l < 4; l++) {
								group_input[N + 1][i][group_count[N + 1][i]][l] = group_input[N][i][j][l];
								if (N == 0) {
									before_regroup[N + 1][i][group_count[N + 1][i]][0] = group_input[N][i][j][4];      //어떤 값들이 재묶음 되었는지 알기위해
									before_regroup[N + 1][i][group_count[N + 1][i]][1] = group_input[N][i + 1][k][4];
									prime_implicants_check[N][i][j] = 1;		//prime_implicants 값들을 확인하기위해
									prime_implicants_check[N][i + 1][k] = 1;
								}
								else if (N == 1) {
									before_regroup[N + 1][i][group_count[N + 1][i]][0] = before_regroup[N][i][j][0];
									before_regroup[N + 1][i][group_count[N + 1][i]][1] = before_regroup[N][i][j][1];
									before_regroup[N + 1][i][group_count[N + 1][i]][2] = before_regroup[N][i + 1][k][0];
									before_regroup[N + 1][i][group_count[N + 1][i]][3] = before_regroup[N][i + 1][k][1];
									prime_implicants_check[N][i][j] = 1;
									prime_implicants_check[N][i + 1][k] = 1;
								}
								else if (N == 2) {
									before_regroup[N + 1][i][group_count[N + 1][i]][0] = before_regroup[N][i][j][0];
									before_regroup[N + 1][i][group_count[N + 1][i]][1] = before_regroup[N][i][j][1];
									before_regroup[N + 1][i][group_count[N + 1][i]][2] = before_regroup[N][i][j][2];
									before_regroup[N + 1][i][group_count[N + 1][i]][3] = before_regroup[N][i][j][3];
									before_regroup[N + 1][i][group_count[N + 1][i]][4] = before_regroup[N][i + 1][k][0];
									before_regroup[N + 1][i][group_count[N + 1][i]][5] = before_regroup[N][i + 1][k][1];
									before_regroup[N + 1][i][group_count[N + 1][i]][6] = before_regroup[N][i + 1][k][2];
									before_regroup[N + 1][i][group_count[N + 1][i]][7] = before_regroup[N][i + 1][k][3];
									prime_implicants_check[N][i][j] = 1;
									prime_implicants_check[N][i + 1][k] = 1;
								}
								else if (N == 3) {
									before_regroup[N + 1][i][group_count[N + 1][i]][0] = before_regroup[N][i][j][0];
									before_regroup[N + 1][i][group_count[N + 1][i]][1] = before_regroup[N][i][j][1];
									before_regroup[N + 1][i][group_count[N + 1][i]][2] = before_regroup[N][i][j][2];
									before_regroup[N + 1][i][group_count[N + 1][i]][3] = before_regroup[N][i][j][3];
									before_regroup[N + 1][i][group_count[N + 1][i]][4] = before_regroup[N][i][j][4];
									before_regroup[N + 1][i][group_count[N + 1][i]][5] = before_regroup[N][i][j][5];
									before_regroup[N + 1][i][group_count[N + 1][i]][6] = before_regroup[N][i][j][6];
									before_regroup[N + 1][i][group_count[N + 1][i]][7] = before_regroup[N][i][j][7];
									before_regroup[N + 1][i][group_count[N + 1][i]][8] = before_regroup[N][i + 1][k][0];
									before_regroup[N + 1][i][group_count[N + 1][i]][9] = before_regroup[N][i + 1][k][1];
									before_regroup[N + 1][i][group_count[N + 1][i]][10] = before_regroup[N][i + 1][k][2];
									before_regroup[N + 1][i][group_count[N + 1][i]][11] = before_regroup[N][i + 1][k][3];
									before_regroup[N + 1][i][group_count[N + 1][i]][12] = before_regroup[N][i + 1][k][4];
									before_regroup[N + 1][i][group_count[N + 1][i]][13] = before_regroup[N][i + 1][k][5];
									before_regroup[N + 1][i][group_count[N + 1][i]][14] = before_regroup[N][i + 1][k][6];
									before_regroup[N + 1][i][group_count[N + 1][i]][15] = before_regroup[N][i + 1][k][7];
									prime_implicants_check[N][i][j] = 1;
									prime_implicants_check[N][i + 1][k] = 1;
								}
								if (group_input[N][i][j][l] != group_input[N][i + 1][k][l]) {   //재묶음된 2개의 값
									group_input[N + 1][i][group_count[N + 1][i]][l] = 2;
								}
							}
							group_count[N + 1][i]++;
						}
					}
				}
			}
			if (N == 2) {		//오류수정
				int count = 0;
				for (int i = 0; i < 16; i++) {
					if (input[i] == 1) {
						count++;
					}
				}
				if (count == 16) {
					group_count[3][0] = 32;
					all_check = 123456;
				}
			}
		}
	}
	for (int N = 0; N < 4; N++) {		//출력문
		if (group_count[N + 1][0] != 0 || group_count[N + 1][1] != 0 || group_count[N + 1][2] != 0 || group_count[N + 1][3] != 0 || group_count[N + 1][4] != 0) {
			cout << "========================================" << endl; //첫번째 재묶음
			for (int i = 0; i < 5; i++) {
				if (group_count[N + 1][i] != 0) {
					cout << "1의 개수가 " << i << "개-------------------------" << endl;
					for (int j = 0; j < group_count[N + 1][i]; j++) {
						int check = 0;
						for (int j_2 = j - 1; j_2 >= 0; j_2--) {
							int count = 0;
							for (int k_2 = 0; k_2 < 4; k_2++) {
								if (group_input[N + 1][i][j][k_2] == group_input[N + 1][i][j_2][k_2]) { count++; }
							}
							if (count == 4) { j_2 = -1; check = 1; }
						}
						if (check != 1) {
							for (int k = 0; k < 4; k++) {
								if (group_input[N + 1][i][j][k] == 2) { cout << "-"; }
								else   cout << group_input[N + 1][i][j][k];
							}
							if (N == 0) {
								cout << "     (" << before_regroup[N + 1][i][j][0] << ", " << before_regroup[N + 1][i][j][1] << ")";
							}
							else if (N == 1) {
								cout << "     (" << before_regroup[N + 1][i][j][0] << ", " << before_regroup[N + 1][i][j][1] << ", " << before_regroup[N + 1][i][j][2] << ", " << before_regroup[N + 1][i][j][3] << ")";
							}
							else if (N == 2) {
								cout << "     (" << before_regroup[N + 1][i][j][0] << ", " << before_regroup[N + 1][i][j][1] << ", " << before_regroup[N + 1][i][j][2] << ", " << before_regroup[N + 1][i][j][3]
									<< ", " << before_regroup[N + 1][i][j][4] << ", " << before_regroup[N + 1][i][j][5] << ", " << before_regroup[N + 1][i][j][6] << ", " << before_regroup[N + 1][i][j][7] << ")";
							}
							else if (N == 3) {
								cout << "     (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15)";
								j = group_count[N + 1][i];
							}
							cout << endl;
						}
					}
				}
			}
		}
	}
	cout << endl;
}

void Prime_implicants_f() {	//prime implicants 추출
	if (all_print == 5) {
		if (all_check == 2) {
			for (int k = 0; k < 4; k++) {
				prime_implicants[1][0][k] = 2;
			}
			for (int k = 0; k < 16; k++) {
				prime_implicants[1][0][4 + k] = k;
			}
			prime_count[1]--;
		}
		else {
			for (int N = 0; N < 5; N++) {		//prime check를 통해서 그룹배열에서 프라임값들을 뽑아낸다
				for (int i = 0; i < 5; i++) {
					for (int j = 0; j < group_count[N][i]; j++) {
						if (prime_implicants_check[N][i][j] != 1) {
							for (int k = 0; k < 4; k++) {
								prime_implicants[0][prime_count[0]][k] = group_input[N][i][j][k];
							}
							if (N == 0) {
								prime_implicants[0][prime_count[0]][4] = group_input[N][i][j][4];
							}
							else {
								for (int k = 0; k < 16; k++) {
									prime_implicants[0][prime_count[0]][4 + k] = before_regroup[N][i][j][k];
								}
							}	prime_count[0]++;
						}
					}
				}
			}
			for (int k = 0; k < 20; k++) {	//첫번째값은 그냥 넣어주기
				prime_implicants[1][prime_count[1]][k] = prime_implicants[0][0][k];
			}
			prime_count[1]++;
			for (int i = 1; i < prime_count[0]; i++) {		//prime_implicants 값들중에 중복된 값 제거
				int clear = 0;
				for (int j = i - 1; j >= 0; j--) {
					int same = 0;
					for (int k = 0; k < 4; k++) {
						if (prime_implicants[0][i][k] == prime_implicants[0][j][k]) {
							same++;
						}
					}
					if (same == 4) {
						clear = 1;
					}
				}
				if (clear != 1) {
					for (int k = 0; k < 20; k++) {
						prime_implicants[1][prime_count[1]][k] = prime_implicants[0][i][k];
					}
					prime_count[1]++;
				}
			}
		}
	}
	cout << "Prime Implicants 값" << endl;		//출력
	for (int i = 0; i < prime_count[1]; i++) {
		for (int j = 0; j < 4; j++) {
			if (prime_implicants[1][i][j] == 2) {
				cout << "-";
			}
			else	cout << prime_implicants[1][i][j];
		}
		cout << "     (, ";
		for (int j = 0; j < 16; j++) {
			if (j > 0) {
				if (prime_implicants[1][i][4 + j] != 0) {
					cout << prime_implicants[1][i][4 + j];
					cout << ", ";
				}
			}
			else {
				cout << prime_implicants[1][i][4 + j];
				cout << ", ";
			}
		}
		cout << ")" << endl;
	}
}

void graph_f() {
	int count = 0;		//prime_implicants의 값들의 개수	
	int hide_count = 0;		//z변수 개수
	int f_z_count = 0;		//f_z변수 개수
	int final_count = 0;		//final 변수 개수
	for (int i = 0; i < prime_count[1]; i++) {		//prime의 저장된 수들을 graph prime 에 저장
		graph_prime[prime_implicants[1][i][4]] = 1;
		for (int j = 5; j < 20; j++) {
			if (prime_implicants[1][i][j] != 0) {
				graph_prime[prime_implicants[1][i][j]] = 1;
			}
		}
	}
	for (int i = 0; i < 16; i++) {		//x배열에 로 저장
		x[i] = 16;
		if (graph_prime[i] == 1) {
			x[count] = i;
			count++;
		}
	}
	int essential_pi_count = 0;		//essential pi 추출, prime 값들끼리 비교해서 중복되는 값들을 저장
	for (int i = 0; i < prime_count[1] - 1; i++) {
		for (int j = 0; j < 16; j++) {
			for (int k = i + 1; k < prime_count[1]; k++) {
				for (int l = 0; l < 16; l++) {
					if ((prime_implicants[1][i][4 + j] != 0 || j == 0) && (prime_implicants[1][k][4 + l] != 0 || l == 0)) {
						if (prime_implicants[1][i][4 + j] == prime_implicants[1][k][4 + l]) {
							essential_pi[essential_pi_count] = prime_implicants[1][i][4 + j];
							essential_pi_count++;
						}
					}
				}
			}
		}
	}
	for (int i = 0; i < 16; i++) { y[i] = 16; }
	for (int i = 0; i < count; i++) { y[i] = x[i]; }		//x에 저장되어있는 값들을 변경하지 않기위해

	for (int i = 0; i < essential_pi_count; i++) {		//겹쳐지지않게 필요없는 공간은 16으로 저장,
		for (int j = 0; j < count; j++) {
			if (essential_pi[i] == y[j]) {
				y[j] = 16;
			}
		}
	}
	for (int i = 0; i < 16; i++) {		//y에 존재하는 값들을 z에 저장
		if (y[i] != 16) {
			z[hide_count] = y[i];
			hide_count++;
		}
	}
	for (int i = 0; i < prime_count[1]; i++) {		//essential pi를 찾기위해 essential pi를 갖고있는 집합의 모든 요소를 f_z에 저장한다
		for (int j = 0; j < 16; j++) {
			if (prime_implicants[1][i][4 + j] != 0 || j == 0) {
				for (int k = 0; k < hide_count; k++) {
					if (prime_implicants[1][i][4 + j] == z[k]) {
						for (int l = 0; l < 16; l++) {
							if (prime_implicants[1][i][4 + l] != 0 || l == 0) {
								f_z[f_z_count] = prime_implicants[1][i][4 + l];
								f_z_count++;
							}
						}
						j = 16, k = hide_count;
					}
				}
			}
		}
	}
	for (int i = 0; i < f_z_count - 1; i++) {		//f_z에서 중복되는 값들을 제거해준다
		for (int j = i + 1; j < f_z_count; j++) {
			if (f_z[i] != 16) {
				if (f_z[i] == f_z[j]) { f_z[j] = 16; }
			}
		}
	}
	cout << endl << "Essential PI 는";
	for (int i = 0; i < 16; i++) {	//y[] 배열에 essential pi 만 남게된다.
		if (y[i] != 16) {
			cout << " " << y[i];
		}
	}
	cout << "이다!" << endl << endl << "O 가 Essential PI를 나타낸다" << endl;
	for (int i = 0; i < 35; i++) {		//16으로 초기화
		z[i] = 16;
	}
	cout << endl << "삭제된 요소들은 '-'로 나타냈다!" << endl << endl;
	for (int i = 0; i < count; i++) {		//Essential PI에 의해 도달되는 모든 요소들 삭제, 출력
		if (x[i] >= 10) {
			cout << " " << x[i] << "  ";
		}
		else
			cout << "  " << x[i] << "  ";
	}
	cout << endl;
	for (int i = 0; i < count; i++) {
		cout << "-----";
	}
	cout << endl;
	for (int i = 0; i < prime_count[1]; i++) {
		for (int j = 0; j < count; j++) {
			int check = 0;
			for (int k = 0; k < 16; k++) {
				if (x[j] == prime_implicants[1][i][4 + k] && (prime_implicants[1][i][4 + k] != 0 || k == 0)) {
					int check_essential_pi = 0;
					for (int n = 0; n < 16; n++) {
						if (y[n] != 16) {
							if (y[n] == prime_implicants[1][i][4 + k]) {
								cout << "  O  ";
								check_essential_pi = 1;
								check = 1;
							}
						}
					}
					int hide_check = 0;
					for (int n = 0; n < f_z_count; n++) {
						if (f_z[n] == prime_implicants[1][i][4 + k] && check_essential_pi == 0) {
							cout << "  -  ";
							hide_check = 1;
							check = 1;
						}
					}
					if (check_essential_pi == 0 && hide_check == 0) {
						cout << "  X  ";
						check = 1;
					}
				}
			}
			if (check == 0) {
				cout << "     ";
			}
		}
		if (i >= 0) {
			for (int j = 0; j < 16; j++) {
				if (j == 0) {
					cout << "| " << prime_implicants[1][i][4] << ", ";
				}
				else if (prime_implicants[1][i][4 + j] != 0) {
					cout << prime_implicants[1][i][4 + j] << ", ";
				}
			}
		}
		cout << endl;
	}
	for (int i = 0; i < prime_count[1]; i++) {		//논리식 도출, 뽑아낸 값들을 final 변수에 저장
		for (int j = 0; j < 16; j++) {
			for (int k = 0; k < 16; k++) {
				if (y[k] != 16) {
					if (prime_implicants[1][i][4 + j] != 0 || j == 0) {
						if (prime_implicants[1][i][4 + j] == y[k]) {
							for (int l = 0; l < 4; l++) {
								final[final_count][l] = prime_implicants[1][i][l];
							}
							j = 16, k = 16,
								final_count++;
						}
					}
				}
			}
		}
	}
	for (int i = 0; i < prime_count[1]; i++) {		//숨겨진 Essential PI 논리식 도출, 뽑아낸 값들을 final변수에 저장
		int find = 0;
		for (int j = 0; j < 16; j++) {
			for (int k = 0; k < f_z_count; k++) {
				if (prime_implicants[1][i][4 + j] != 0 || j == 0) {
					if (prime_implicants[1][i][4 + j] == f_z[k]) {
						find++;
					}
				}
			}
		}
		if (find == 0) {
			for (int l = 0; l < 4; l++) {
				final[final_count][l] = prime_implicants[1][i][l];
			}
			final_count++;
		}
	}
	cout << endl << "F";
	for (int i = 0; i < final_count; i++) {		//논리식 출력, 자리수에 맞게 알파벳으로 표현
		if (i == 0) {
			cout << " = ";
		}
		else {
			cout << " + ";
		}
		for (int l = 0; l < 4; l++) {
			if (l == 0) {
				if (final[i][l] == 1) {
					cout << "a";
				}
				else if (final[i][l] == 0) {
					cout << "a'";
				}
			}
			else if (l == 1) {
				if (final[i][l] == 1) {
					cout << "b";
				}
				else if (final[i][l] == 0) {
					cout << "b'";
				}
			}
			else if (l == 2) {
				if (final[i][l] == 1) {
					cout << "c";
				}
				else if (final[i][l] == 0) {
					cout << "c'";
				}
			}
			else if (l == 3) {
				if (final[i][l] == 1) {
					cout << "d";
				}
				else if (final[i][l] == 0) {
					cout << "d'";
				}
			}
		}
	}
}

int main(void) {
	input_f();		//입력받을 수 저장
	while (1) {
		change_f();		//입력받은 수 2진수로 변환	
		if (all_print > 0) {
			group_f();		//변환한 값들 1의 개수에 따라 그룹화
			if (all_print > 1) {
				regroup_f();		//그룹화 한 값들을 Changing bit 체크를 통해서 재구
				if (all_print > 2) {
					Prime_implicants_f();		//Prime implicants 추출
					if (all_print > 3) { graph_f(); }		//prime implicants를 이용하여 회로식 도출
				}
			}
		}
		if (all_print == 5) {
			system("cls");
			all_print = 1;
		}
		else {
			key = _getch();
			if (key == 224) {
				key = _getch();
				if (key == DOWN) {
					all_print++;
					if (all_print == 5) { return 0; }
				}
				else if (key == UP) {
					all_print--;
				}
			}system("cls");
		}
	}
}
