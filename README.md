#include <iostream>
#include <vector>
#include <fstream>
#include <string>
#include <sstream>
#include <windows.h>

using namespace std;

ifstream Hi;
void PrintGraph(vector<vector<int>> Graph, int n)
{
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < n; j++)
		{
			cout << Graph[i][j] << " ";
		}
		cout << endl;
	}
}
int NomerMaxElement(vector<int> Rebro, int n, vector<bool> Vers) //Поиск вершины
{
	int max = 0;
	int NomerMax = 0;
	for (int i = 0; i < n; i++)
	{
		if (Rebro[i] > max)
		{
			if (Vers[i] == true)
			{
				max = Rebro[i];
				NomerMax = i + 1;
			}
		}
	}
	return NomerMax;
}
bool Check(vector < vector < int >> Graph, int n) //Проверка на циклы
{
	bool Cycle = false;
	int rebro = 0, stroka = 0, length = n;
	for (int i = 0; i < n; i++)
	{
		stroka = 0;
		for (int j = 0; j < n; j++)
		{
			if (Graph[i][j] == 1)
			{
				rebro++;
				stroka++;
			}
		}
		if (stroka == 0)
		{
			length--;
		}
	}
	rebro = rebro / 2;
	if (rebro >= length)
	{
		Cycle = true;
	}
	return Cycle;

}
bool Del(vector<vector<int>> Graph, int n, int Ver, vector<bool> Vers, vector<int> DelVers, vector<vector<int>> Connect)
{
	bool notAlone = true;
	int f, f1, f2, f3, f4;
	if (Vers[Ver - 1] == true)
	{
		for (int i = 0; i < n; i++)
		{
			Graph[Ver - 1][i] = 0;
			Graph[i][Ver - 1] = 0;
		}
		for (int i = 0; i < n; i++)
		{
			for (int j = 0; j < n; j++)
			{
				if (Graph[i][j] == 1)
				{
					Connect[i].push_back(j + 1);
				}
			}
		}
		for (int i = 0; i < Connect.size(); i++)
		{
			if (Connect[i].size() == 1)
			{
				f = Connect[i][0];
				if (Connect[f - 1].size() == 1)
				{
					for (int k = 0; k < Connect[i].size(); k++)
					{
						if (Connect[f - 1][0] == i + 1)
						{
							notAlone = false;
							break;
						}
					}
				}
			}
		}
		for (int i = 0; i < Connect.size(); i++)
		{
			if (Connect[i].size() == 2)
			{
				f1 = Connect[i][0];
				f2 = Connect[i][1];
				if (Connect[f1 - 1].size() == 1 && Connect[f2 - 1].size() == 1)
				{
					if (Connect[f1 - 1][0] == i + 1 && Connect[f2 - 1][0] == i + 1)
					{
						notAlone = false;
						break;
					}
				}
			}
		}
	}
	int stroka;
	bool flag = false;
	for (int i = 0; i < n; i++)
	{
		stroka = 0;
		for (int j = 0; j < n; j++)
		{
			if (Graph[i][j] == 1)
			{
				stroka++;
			}
		}
		if (stroka == 0)
		{
			for (int k = 0; k < DelVers.size(); k++)
			{
				flag = true;
				if (i != DelVers[k] - 1 && i != Ver - 1)
				{
					notAlone = false;
					break;
				}
			}
			if (flag == false && i != Ver - 1)
			{
				notAlone = false;
				break;
			}
		}
	}
	return notAlone;
}
void DeleteVer(vector<vector<int>>& Graph, int n, int Vertex, vector<bool>& Vertexes)
{
	for (int i = 0; i < n; i++)
	{
		Graph[Vertex - 1][i] = 0;
		Graph[i][Vertex - 1] = 0;
	}
	Vertexes[Vertex - 1] = false;
}
int GetNum()
{
	int N = 0, num;
	string str;
	getline(Hi, str);
	stringstream strstr;
	strstr << str;
	while (strstr >> num)
	{
		N = num;
	}
	return N;
}
void scanning(vector<vector<int>>& Graph)
{
	int num, m = 0, n = 0;
	string str;
	while (!Hi.eof())
	{
		n = 0;
		getline(Hi, str);
		stringstream strstr;
		strstr << str;
		while (strstr >> num)
		{
			Graph[m][n] = num;
			n++;
		}
		m++;
	}
}
void FindVers(vector<vector<int>>& Graph, int N, vector<int> Rebro, vector<vector<int>>& Connect, vector<bool>& Vers, vector<int> DelVers)
{
	int DelVer;
	while (Check(Graph, N))
	{
		DelVer = NomerMaxElement(Rebro, N, Vers);
		if (Del(Graph, N, DelVer, Vers, DelVers, Connect))
		{
			DelVers.push_back(DelVer);
			DeleteVer(Graph, N, DelVer, Vers);
			//PrintGraph(Graph, N); Вывод матрицы после удаления вершины
		}
		else
		{
			Vers[DelVer - 1] = false;
		}
	}
	cout << "Номера вершин, удаление которых позволяет сделать граф деревом: ";
	for (int i = 0; i < DelVers.size(); i++)
	{
		cout << DelVers[i] << " ";
	}
}

int NomerRebra(vector<vector<int>> Graph, int NR, int N)
{
	NR = 0;
	for (int i = 0; i < N; i++)
	{
		for (int j = 0; j < N; j++)
		{
			if (Graph[i][j] == 1)
			{
				NR++;
			}
		}
	}
	return NR / 2;
}
void FillRebro(vector<vector<int>> Graph, vector<int>& Rebro, int N)

{
	for (int i = 0; i < N; i++)
	{
		for (int j = 0; j < N; j++)
		{
			if (Graph[i][j] == 1)
			{
				Rebro[i]++;
			}
		}
	}
}

void Tests(string path, int GraphNumber)
{
	int N, NR = 0;
	Hi.open(path);
	N = GetNum();
	vector<vector<int>> Graph(N, vector<int>(N, 0));
	scanning(Graph);
	Hi.close();
	vector<int> DelVers;
	vector<int> Rebro(N, 0);
	vector<bool> Vers(N, true);
	vector<vector<int>> Connect(N, vector<int>(0, 0));
	NR = NomerRebra(Graph, NR, N);
	FillRebro(Graph, Rebro, N);
	cout << GraphNumber << "-й граф:" << endl;
	FindVers(Graph, N, Rebro, Connect, Vers, DelVers);
	cout << endl;
	cout << endl;
}

int main()
{
	SetConsoleCP(1251);
	SetConsoleOutputCP(1251);
	Tests("Graph1.txt", 1);
	Tests("Graph2.txt", 2);
	Tests("Graph3.txt", 3);
	Tests("Graph4.txt", 4);
	Tests("Graph5.txt", 5);
	cout << endl;
	return 0;
}
