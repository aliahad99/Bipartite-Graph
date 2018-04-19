# Bipartite-Graph
This code is to check whether a graph is bipartite or not


#include <stdio.h>
#include <stdlib.h>

struct node
{
	int data;
	struct Node *next;
};

struct queue
{
	struct node *front, *rear;
};

struct queue* New_Queue(void)
{
	struct queue *q = (struct queue*)malloc(sizeof(struct queue));
	q->front = q->rear = NULL;
	return q;
}

struct node *create_node(int data)
{
   struct node *N = (struct node *)malloc(sizeof(struct node));
	N->data = data;
	N->next = NULL;
	return N;
}

void Enqueue(struct queue *q, int data)
{
   struct node *newnode = create_node(data);

	if (q->rear == NULL)
   {
		q->front = q->rear = newnode;
		return;
	}

	q->rear->next = newnode;
	q->rear = newnode;
}

int Dequeue(struct queue *q)
{
	if (q->front == NULL)
		return 0;

	struct node *temp = q->front;
	int element = temp->data;
	q->front = q->front->next;

	free(temp);

	if (q->front == NULL)
		q->rear = NULL;

	return element;
}


int Bipartite_Util(int *g, int n, int index, int *color)
{
	color[index] = 1;

	struct queue *temp = New_Queue();
	Enqueue(temp, index);

	while (temp->front != NULL)
   {
		int i = Dequeue(temp);

		if (g[i* n + i] == 1)
			return 0;

      int j;
		for (j = 0; j < n; j++)
      {
			if ((color[j] == -1) && (g[i * n + j] == 1))
			{
				color[j] = 1 - color[i];
				Enqueue(temp, j);
			}

			else if ((color[j] == color[i]) && (g[i* n + j] == 1))
				return 0;
		}
	}

	return 1;
}


int Check_Bipartite(int *g, int n)
{
	int *temp = (int *)malloc(n * sizeof(int));

	int i;
	for (i = 0; i < n; i++)
		temp[i] = -1;

	for (i = 0; i < n; i++)
		if (temp[i] == -1)
			if (Bipartite_Util(g, n, i, temp) == 0)
				return 0;
	return 1;
}

int main(void)
{
	int N, i;
	scanf("%d", &N);

	for(i = 0; i < N; i++)
   {
		int n;
		scanf("%d", &n);

		int *G = (int *)malloc(n * n * sizeof(int));

		int j;
		for (j = 0; j < n * n; j++)
			G[j] = 0;

		int n1, n2;
		scanf("%d", &n1);

		while (n1 != -1)
      {
			scanf("%d", &n2);
			G[(n1 - 1) * n + (n2 - 1)] = G[(n2 - 1) * n + (n1 - 1)] = 1;
			scanf("%d", &n1);
		}

		if (Check_Bipartite(G, n))
			printf("YES\n");
		else
			printf("NO\n");

		free(G);

	}
}
