#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <locale.h>
#include <time.h>

#define MAX_COROINHAS 35
#define MAX_NOME 50

int dia_da_semana(int d, int m, int a) {
    if (m < 3) { m += 12; a -= 1; }
    int k = a % 100;
    int j = a / 100;
    int h = (d + 13*(m + 1)/5 + k + k/4 + j/4 + 5*j) % 7;
    return (h + 6) % 7; // 0=domingo
}

int dias_no_mes(int mes, int ano) {
    int dias_mes[] = {31,28,31,30,31,30,31,31,30,31,30,31};
    if ((ano % 4 == 0 && ano % 100 != 0) || (ano % 400 == 0))
        dias_mes[1] = 29;
    return dias_mes[mes - 1];
}

void embaralhar(char nomes[][MAX_NOME], int n) {
    for (int i=n-1;i>0;i--) {
        int j=rand()%(i+1);
        char temp[MAX_NOME];
        strcpy(temp, nomes[i]);
        strcpy(nomes[i], nomes[j]);
        strcpy(nomes[j], temp);
    }
}

int main() {
    system("cls");
    setlocale(LC_ALL,"Portuguese");
    srand(time(NULL));

    char coroinhas[MAX_COROINHAS][MAX_NOME] = {
        "Ana","Lucas","Sofia","Miguel","Laura","Gabriel","Helena",
        "Rafael","Isabela","Pedro","J�lia","Daniel","Mariana","Andr�",
        "Beatriz","Thiago","Camila","Gustavo","Vit�ria","Bruno","Alice",
        "Leonardo","Clara","Matheus","Bianca","Felipe","Valentina","Eduardo",
        "L�via","Diego","Lorena","Henrique","Cec�lia","Jo�o","Emanuelly"
    };
    int total_coroinhas = MAX_COROINHAS;

    int mes;
    printf("Digite o m�s (1-12): ");
    scanf("%d", &mes);

    char nome_mes[20];
    switch(mes){
        case 1: strcpy(nome_mes,"Janeiro"); break;
        case 2: strcpy(nome_mes,"Fevereiro"); break;
        case 3: strcpy(nome_mes,"Mar�o"); break;
        case 4: strcpy(nome_mes,"Abril"); break;
        case 5: strcpy(nome_mes,"Maio"); break;
        case 6: strcpy(nome_mes,"Junho"); break;
        case 7: strcpy(nome_mes,"Julho"); break;
        case 8: strcpy(nome_mes,"Agosto"); break;
        case 9: strcpy(nome_mes,"Setembro"); break;
        case 10: strcpy(nome_mes,"Outubro"); break;
        case 11: strcpy(nome_mes,"Novembro"); break;
        case 12: strcpy(nome_mes,"Dezembro"); break;
        default: strcpy(nome_mes,"M�s Inv�lido"); break;
    }

    printf("\nEscala de Coroinhas - %s\n", nome_mes);
    printf("---------------------------------------------\n");

    // Ano atual
    time_t t = time(NULL);
    struct tm *data_atual = localtime(&t);
    int ano = data_atual->tm_year + 1900;

    int dias = dias_no_mes(mes, ano);

    int idx_ultimo_dia[MAX_COROINHAS];
    for(int i=0;i<MAX_COROINHAS;i++) idx_ultimo_dia[i]=-2;

    for(int dia=1; dia<=dias; dia++){
        int semana = dia_da_semana(dia, mes, ano);
        int qtd = 0;

        if(semana==0) qtd=7; // domingo
        else if(semana==1) qtd=2; // segunda
        else if(semana==4) qtd=4; // quinta
        else continue;

        if(semana==0){ // domingo com manh� e noite
            char usados_dia[qtd*2][MAX_NOME];
            int total_usados = 0;

            for(int e=0;e<2;e++){ // manh� e noite
                printf("%02d/%02d - Domingo/%s: ", dia, mes, (e==0)?"Manh�":"Noite");
                char selecionados[qtd][MAX_NOME];
                int count=0;

                int indices[MAX_COROINHAS];
                for(int i=0;i<MAX_COROINHAS;i++) indices[i]=i;
                for(int i=MAX_COROINHAS-1;i>0;i--){
                    int j=rand()%(i+1);
                    int tmp=indices[i]; indices[i]=indices[j]; indices[j]=tmp;
                }

                for(int i=0;i<MAX_COROINHAS && count<qtd;i++){
                    int idx=indices[i];
                    int repetido = 0;
                    for(int u=0;u<total_usados;u++){
                        if(strcmp(usados_dia[u], coroinhas[idx])==0){
                            repetido=1;
                            break;
                        }
                    }
                    if(!repetido && dia - idx_ultimo_dia[idx]>1){
                        strcpy(selecionados[count], coroinhas[idx]);
                        strcpy(usados_dia[total_usados], coroinhas[idx]);
                        total_usados++;
                        idx_ultimo_dia[idx]=dia;
                        count++;
                    }
                }

                for(int i=0;i<MAX_COROINHAS && count<qtd;i++){
                    int idx=indices[i];
                    int repetido=0;
                    for(int u=0;u<total_usados;u++){
                        if(strcmp(usados_dia[u], coroinhas[idx])==0){
                            repetido=1; break;
                        }
                    }
                    if(!repetido && dia - idx_ultimo_dia[idx]>=0){
                        strcpy(selecionados[count], coroinhas[idx]);
                        strcpy(usados_dia[total_usados], coroinhas[idx]);
                        total_usados++;
                        idx_ultimo_dia[idx]=dia;
                        count++;
                    }
                }

                for(int i=0;i<qtd;i++){
                    printf("%s", selecionados[i]);
                    if(i<qtd-1) printf(", ");
                }
                printf("\n");
            }

        } else { // segunda ou quinta
            printf("%02d/%02d - ", dia, mes);
            switch(semana){
                case 1: printf("Segunda: "); break;
                case 4: printf("Quinta: "); break;
            }

            char selecionados[qtd][MAX_NOME];
            int count=0;

            int indices[MAX_COROINHAS];
            for(int i=0;i<MAX_COROINHAS;i++) indices[i]=i;
            for(int i=MAX_COROINHAS-1;i>0;i--){
                int j=rand()%(i+1);
                int tmp=indices[i]; indices[i]=indices[j]; indices[j]=tmp;
            }

            for(int i=0;i<MAX_COROINHAS && count<qtd;i++){
                int idx=indices[i];
                if(dia - idx_ultimo_dia[idx]>1){
                    strcpy(selecionados[count], coroinhas[idx]);
                    idx_ultimo_dia[idx]=dia;
                    count++;
                }
            }

            for(int i=0;i<MAX_COROINHAS && count<qtd;i++){
                int idx=indices[i];
                if(dia - idx_ultimo_dia[idx]>=0){
                    strcpy(selecionados[count], coroinhas[idx]);
                    idx_ultimo_dia[idx]=dia;
                    count++;
                }
            }

            for(int i=0;i<qtd;i++){
                printf("%s", selecionados[i]);
                if(i<qtd-1) printf(", ");
            }
            printf("\n");
        }
    }

    printf("---------------------------------------------\n");
    return 0;
}
//Criador: Joaquim Soares
//@_joaquimsoares1
