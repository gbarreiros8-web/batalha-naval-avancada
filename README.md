# batalha-naval-avancada
Fundamentos e Técnicas Avançadas

git clone https://github.com/seu-usuario/batalha-naval-avancada.git
cd batalha-naval-avancada

mkdir src
# cole main.c em src/
# adicione .gitignore e README.md
git add .
git commit -m "Versão inicial: fundamentos e técnicas avançadas"
git push origin main

batalha-naval-avancada/
├── README.md
├── src/
│   └── main.c
└── .gitignore

#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define SIZE 10
#define NUM_SHIPS 5

typedef struct {
    int x, y;
} Position;

typedef struct {
    char board[SIZE][SIZE];
    int shipsRemaining;
} PlayerBoard;

void initBoard(PlayerBoard *pb){
    pb->shipsRemaining = 0;
    for(int i=0;i<SIZE;i++)
        for(int j=0;j<SIZE;j++)
            pb->board[i][j] = '~';
}

void printBoard(PlayerBoard *pb, int hideShips){
    printf("  ");
    for(int i=0;i<SIZE;i++) printf("%d ", i);
    printf("\n");
    for(int i=0;i<SIZE;i++){
        printf("%d ", i);
        for(int j=0;j<SIZE;j++){
            char c = pb->board[i][j];
            if(hideShips && c=='S') c='~';
            printf("%c ", c);
        }
        printf("\n");
    }
}

int isValidPosition(PlayerBoard *pb, int x, int y, int size, char dir){
    if(dir=='H') {
        if(y+size>SIZE) return 0;
        for(int i=0;i<size;i++) if(pb->board[x][y+i]=='S') return 0;
    } else {
        if(x+size>SIZE) return 0;
        for(int i=0;i<size;i++) if(pb->board[x+i][y]=='S') return 0;
    }
    return 1;
}

void placeShip(PlayerBoard *pb, int size){
    int x,y;
    char dir;
    do{
        x=rand()%SIZE;
        y=rand()%SIZE;
        dir = (rand()%2)? 'H':'V';
    } while(!isValidPosition(pb,x,y,size,dir));

    for(int i=0;i<size;i++){
        if(dir=='H') pb->board[x][y+i]='S';
        else pb->board[x+i][y]='S';
    }
    pb->shipsRemaining++;
}

int attack(PlayerBoard *pb, int x, int y){
    if(pb->board[x][y]=='S'){
        pb->board[x][y]='X';
        pb->shipsRemaining--;
        return 1; // acerto
    } else if(pb->board[x][y]=='~'){
        pb->board[x][y]='O';
        return 0; // erro
    }
    return -1; // já atacado
}

Position randomMove(){
    Position p;
    p.x = rand()%SIZE;
    p.y = rand()%SIZE;
    return p;
}

int main(){
    srand(time(NULL));

    PlayerBoard player, computer;
    initBoard(&player);
    initBoard(&computer);

    // Colocando navios automaticamente
    int shipSizes[NUM_SHIPS] = {5,4,3,3,2};
    for(int i=0;i<NUM_SHIPS;i++){
        placeShip(&player, shipSizes[i]);
        placeShip(&computer, shipSizes[i]);
    }

    int turn = 1;
    int x,y;
    while(player.shipsRemaining>0 && computer.shipsRemaining>0){
        if(turn==1){
            printBoard(&computer,1);
            printf("Seu turno! Digite coordenadas (linha coluna): ");
            scanf("%d %d",&x,&y);
        } else {
            Position p;
            do { p=randomMove(); } while(attack(&player,p.x,p.y)==-1);
            x=p.x; y=p.y;
            printf("Computador ataca: %d %d\n",x,y);
        }

        int result = (turn==1)? attack(&computer,x,y) : attack(&player,x,y);
        if(result==1) printf("Acertou!\n");
        else if(result==0) printf("Água!\n");

        if(player.shipsRemaining==0) { printf("Computador venceu!\n"); break;}
        if(computer.shipsRemaining==0) { printf("Você venceu!\n"); break;}

        turn = (turn==1)? 2 : 1;
    }

    return 0;
}


