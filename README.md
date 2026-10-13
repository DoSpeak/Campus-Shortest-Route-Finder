
#include <stdio.h>
#include <string.h>

#define MAX 20
#define INF 999999

int graph[MAX][MAX];
int n;
char location[MAX][50];

int dist[MAX];
int parent[MAX];
int visited[MAX];

int graphEntered = 0;
int sourceSelected = 0;
int shortestCalculated = 0;
int source = -1;

/* Function to display campus locations */
void displayLocations()
{
    int i;

    printf("\nCampus Locations:\n");

    for (i = 0; i < n; i++)
    {
        printf("%d. %s\n", i + 1, location[i]);
    }
}

/* Function to enter the campus graph */
void enterCampusGraph()
{
    int i, j, edges;
    int u, v, weight;

    printf("\nEnter number of campus locations (1-%d): ", MAX);
    scanf("%d", &n);

    if (n < 1 || n > MAX)
    {
        printf("Invalid number of locations!\n");
        n = 0;
        return;
    }

    /* Clear previous graph data */
    for (i = 0; i < n; i++)
    {
        printf("Enter name of location %d: ", i + 1);
        scanf(" %49[^\n]", location[i]);
    }

    /* Initialize adjacency matrix */
    for (i = 0; i < n; i++)
    {
        for (j = 0; j < n; j++)
        {
            if (i == j)
                graph[i][j] = 0;
            else
                graph[i][j] = INF;
        }
    }

    printf("\nEnter number of roads: ");
    scanf("%d", &edges);

    if (edges < 0 || edges > n * (n - 1) / 2)
    {
        printf("Invalid number of roads!\n");
        n = 0;
        return;
    }

    printf("\nAvailable locations:\n");
    displayLocations();

    printf("\nEnter road details using location numbers.\n");
    printf("Roads are assumed to be bidirectional.\n");

    for (i = 0; i < edges; i++)
    {
        printf("\nRoad %d:\n", i + 1);

        printf("Enter starting location number: ");
        scanf("%d", &u);

        printf("Enter destination location number: ");
        scanf("%d", &v);

        printf("Enter distance (non-negative): ");
        scanf("%d", &weight);

        if (u < 1 || u > n || v < 1 || v > n ||
            u == v || weight < 0)
        {
            printf("Invalid road details! Enter this road again.\n");
            i--;
            continue;
        }

        /* Keep the smallest weight for duplicate roads */
        if (weight < graph[u - 1][v - 1])
        {
            graph[u - 1][v - 1] = weight;
            graph[v - 1][u - 1] = weight;
        }

        printf("Road added successfully!\n");
    }

    graphEntered = 1;
    sourceSelected = 0;
    shortestCalculated = 0;

    printf("\nCampus graph created successfully!\n");
}

/* Function to display adjacency matrix */
void displayAdjacencyMatrix()
{
    int i, j;

    if (!graphEntered)
    {
        printf("\nPlease enter the campus graph first!\n");
        return;
    }

    printf("\n========== ADJACENCY MATRIX ==========\n");
    printf("%-22s", "Location");

    for (i = 0; i < n; i++)
        printf("%-12d", i + 1);

    printf("\n");

    for (i = 0; i < n; i++)
    {
        printf("%-22s", location[i]);

        for (j = 0; j < n; j++)
        {
            if (graph[i][j] == INF)
                printf("%-12s", "INF");
            else
                printf("%-12d", graph[i][j]);
        }

        printf("\n");
    }
}

/* Function to select source location */
void selectSourceLocation()
{
    int s;

    if (!graphEntered)
    {
        printf("\nPlease enter the campus graph first!\n");
        return;
    }

    displayLocations();

    printf("\nEnter source location number: ");
    scanf("%d", &s);

    if (s < 1 || s > n)
    {
        printf("Invalid source location!\n");
        return;
    }

    source = s - 1;
    sourceSelected = 1;
    shortestCalculated = 0;

    printf("\nSelected Source: %s\n", location[source]);
}

/* Dijkstra's shortest-path algorithm */
void dijkstra()
{
    int i, j;
    int minDistance, u;

    if (!graphEntered)
    {
        printf("\nPlease enter the campus graph first!\n");
        return;
    }

    if (!sourceSelected)
    {
        printf("\nPlease select the source location first!\n");
        return;
    }

    /* Initialize arrays */
    for (i = 0; i < n; i++)
    {
        dist[i] = INF;
        parent[i] = -1;
        visited[i] = 0;
    }

    dist[source] = 0;

    /* Process all vertices */
    for (i = 0; i < n; i++)
    {
        minDistance = INF;
        u = -1;

        /* Find the unvisited vertex with minimum distance */
        for (j = 0; j < n; j++)
        {
            if (!visited[j] && dist[j] < minDistance)
            {
                minDistance = dist[j];
                u = j;
            }
        }

        /* No more reachable vertices */
        if (u == -1)
            break;

        visited[u] = 1;

        /* Update distances of adjacent vertices */
        for (j = 0; j < n; j++)
        {
            if (!visited[j] &&
                graph[u][j] != INF &&
                dist[u] != INF &&
                dist[u] + graph[u][j] < dist[j])
            {
                dist[j] = dist[u] + graph[u][j];
                parent[j] = u;
            }
        }
    }

    shortestCalculated = 1;

    printf("\nDijkstra's Algorithm executed successfully!\n");
}

/* Recursive function to print the shortest path */
void printPath(int vertex)
{
    if (vertex == -1)
        return;

    printPath(parent[vertex]);

    printf("%s", location[vertex]);
}

/* Function to display shortest paths */
void displayShortestPaths()
{
    int i;

    if (!shortestCalculated)
    {
        printf("\nPlease find shortest distances first (Option 4)!\n");
        return;
    }

    printf("\n========== SHORTEST PATHS ==========\n");
    printf("Source: %s\n\n", location[source]);

    printf("%-25s %-18s %s\n",
           "Destination", "Distance", "Shortest Path");

    printf("--------------------------------------------------------------------------\n");

    for (i = 0; i < n; i++)
    {
        if (i == source)
        {
            printf("%-25s %-18d ",
                   location[i], 0);

            printf("%s\n", location[source]);
        }
        else if (dist[i] == INF)
        {
            printf("%-25s %-18s %s\n",
                   location[i], "INF", "Unreachable");
        }
        else
        {
            printf("%-25s %-18d ",
                   location[i], dist[i]);

            printPath(i);
            printf("\n");
        }
    }
}

/* Function to display distances only */
void displayDistances()
{
    int i;

    if (!shortestCalculated)
    {
        printf("\nPlease find shortest distances first (Option 4)!\n");
        return;
    }

    printf("\n========== DISTANCES FROM SOURCE ==========\n");
    printf("Source Location: %s\n\n", location[source]);

    for (i = 0; i < n; i++)
    {
        if (dist[i] == INF)
        {
            printf("%s: Unreachable\n", location[i]);
        }
        else
        {
            printf("%s: %d units\n",
                   location[i], dist[i]);
        }
    }
}

/* Main function */
int main()
{
    int choice;

    do
    {
        printf("\n\n============================================\n");
        printf("   CAMPUS SHORTEST ROUTE FINDER\n");
        printf("       DIJKSTRA'S ALGORITHM\n");
        printf("============================================\n");

        printf("1. Enter Campus Graph\n");
        printf("2. Display Adjacency Matrix\n");
        printf("3. Select Source Location\n");
        printf("4. Find Shortest Distance\n");
        printf("5. Display Shortest Paths\n");
        printf("6. Display Distance from Source to All Locations\n");
        printf("7. Exit\n");

        printf("\nEnter your choice: ");
        scanf("%d", &choice);

        switch (choice)
        {
            case 1:
                enterCampusGraph();
                break;

            case 2:
                displayAdjacencyMatrix();
                break;

            case 3:
                selectSourceLocation();
                break;

            case 4:
                dijkstra();
                break;

            case 5:
                displayShortestPaths();
                break;

            case 6:
                displayDistances();
                break;

            case 7:
                printf("\nThank you for using Campus Shortest Route Finder!\n");
                break;

            default:
                printf("\nInvalid choice! Please enter 1 to 7.\n");
        }

    } while (choice != 7);

    return 0;
}
