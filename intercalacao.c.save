//
// Created by Vanessa Braganholo.
// Updated by Raphael Oliveira.

#ifdef _MSC_VER
#define _CRT_SECURE_NO_WARNINGS
#endif

#include "intercalacao.h"
#include "nomes.h"
#include "cliente.h"
#include <limits.h>
#include <string.h>
#include <stdlib.h>

typedef struct vetor{
    TCliente *cli;
    FILE *f;
}TVet;

/*
 * Definicao de tipo para no de arvore de vencedores
 */
typedef struct No {
    TCliente *vencedor;
    struct No *endVencedor;
    FILE *f;
    struct No *pai;
    struct No *dir;
    struct No *esq;
} TNo;

void intercalacao_basico(char *nome_arquivo_saida, int num_p, Nomes *nome_particoes) {

    int fim = 0; //variavel que controla fim do procedimento
    FILE *out; //declara ponteiro para arquivo

    //abre arquivo de saida para escrita
    if ((out = fopen(nome_arquivo_saida, "wb")) == NULL) {
        printf("Erro ao abrir arquivo de saida\n");
    } else {
        //cria vetor de particoes
        TVet v[num_p];

        //abre arquivos das particoes, colocando variavel de arquivo no campo f do vetor
        //e primeiro cliente do arquivo no campo cli do vetor
        for (int i=0; i < num_p; i++) {
            v[i].f = fopen(nome_particoes->nome, "rb");
            if (v[i].f != NULL) {
                TCliente *c = le_cliente(v[i].f);
                if (c == NULL) {
                    //arquivo estava vazio
                    //coloca HIGH VALUE nessa posicao do vetor
                    v[i].cli = cliente(INT_MAX, "");
                }
                else {
                    //conseguiu ler cliente, coloca na posicao atual do vetor
                    v[i].cli = c;
                }
            }
            else {
                fim = 1;
            }
            nome_particoes = nome_particoes->prox;
        }

        while (!(fim)) { //conseguiu abrir todos os arquivos
            int menor = INT_MAX;
            int pos_menor;
            //encontra o cliente com menor chave no vetor
            for(int i = 0; i < num_p; i++){
                if(v[i].cli->cod_cliente < menor){
                    menor = v[i].cli->cod_cliente;
                    pos_menor = i;
                }
            }
            if (menor == INT_MAX) {
                fim = 1; //terminou processamento
            } else {
                //salva cliente no arquivo de saida
                salva_cliente(v[pos_menor].cli, out);
                //atualiza posicao pos_menor do vetor com proximo cliente do arquivo
                TCliente *c = le_cliente(v[pos_menor].f);
                if (c == NULL) {
                    //arquivo estava vazio
                    //coloca HIGH VALUE nessa posicao do vetor
                    v[pos_menor].cli = cliente(INT_MAX, "");
                }
                else {
                    v[pos_menor].cli = c;
                }

            }
        }

        //fecha arquivos das particoes de entrada
        for(int i = 0; i < num_p; i++){
            fclose(v[i].f);
        }
        //fecha arquivo de saida
        fclose(out);
    }
}

//funções auxiliares para ABV(criar no, construir arvore, atualizar arvore e liberar arvore)
TNo* criarNo(TNo *esq, TNo *dir) {
    TNo *no = (TNo*)malloc(sizeof(TNo)); //aloca um novo no
    no->esq = esq;
    no->dir = dir;
    no->f = NULL;
    no->pai = NULL;
    if (esq->vencedor->cod_cliente <= dir->vencedor->cod_cliente) {
        no->vencedor = esq->vencedor;
        no->endVencedor = esq->endVencedor; //ponteiro para a folha de onde o vencedor veio
    } else {
        no->vencedor = dir->vencedor;
        no->endVencedor = dir->endVencedor; //ponteiro para a folha de onde o vencedor veio
    }
    esq->pai = no;
    dir->pai = no;
    return no;
}

TNo* construirArvore(TNo **nos, int n) {
    if (n == 1) {
        return nos[0]; //se tem so 1 no ele é a raiz
    }

    int num_pais = (n + 1) / 2;
    TNo **pais = (TNo**)malloc(sizeof(TNo*) * num_pais); //aloca todos os novos pais temporários ate criar a proxima camada
    int indice = 0;
    for (int i = 0; i < n; i += 2) {
        if (i + 1 < n) {
            pais[indice++] = criarNo(nos[i], nos[i+1]); //par de nós cria pai
        } else {
            pais[indice++] = nos[i]; //no impar sobe direto
        }
    }
    TNo *raiz = construirArvore(pais, num_pais);

    free(pais);
    return raiz;
}

void atualizarArvore(TNo *folha) {
    TNo *atual = folha->pai;
    while (atual != NULL) { //enquanto nao chega na raiz...
        TNo *esq = atual->esq;
        TNo *dir = atual->dir;
        if (esq->vencedor->cod_cliente <= dir->vencedor->cod_cliente) { //compara filhos
            atual->vencedor = esq->vencedor;
            atual->endVencedor = esq->endVencedor;
        } else {
            atual->vencedor = dir->vencedor;
            atual->endVencedor = dir->endVencedor;
        }
        atual = atual->pai; //sobe para o pai
    }
}

void intercalacao_arv_vencedores(char *nome_arquivo_saida, int num_p, Nomes *nome_particoes) {
    FILE *out; //declara ponteiro para arquivo

    if ((out = fopen(nome_arquivo_saida, "wb")) == NULL) {
        printf("Erro ao abrir arquivo de saida\n");
        return;
    }

    TNo **folhas = (TNo**)malloc(sizeof(TNo*) * num_p); //aloca vetor de folhas
    Nomes *atual = nome_particoes;
    int erro = 0;

    for (int i = 0; i < num_p; i++) {
        folhas[i] = (TNo*)malloc(sizeof(TNo)); //aloca folha
        //inicicaliza ponteiros
        folhas[i]->esq = NULL;
        folhas[i]->dir = NULL;
        folhas[i]->pai = NULL;
        folhas[i]->endVencedor = folhas[i]; //guarda a origem pra casa seja vencedor
        folhas[i]->vencedor = NULL;
        folhas[i]->f = fopen(atual->nome, "rb"); //abre arquivo da particao
        if (folhas[i]->f != NULL) {
            TCliente *c = le_cliente(folhas[i]->f); //tenta ler o primeiro regristo
            if (c == NULL) {
                folhas[i]->vencedor = cliente(INT_MAX, ""); //marca fim do arquivo, HIGH VALUE
            } else {
                folhas[i]->vencedor = c;
            }
        } else {
            folhas[i]->vencedor = cliente(INT_MAX, ""); //HIGH VALUE e marca erro
            erro = 1;
        }
        atual = atual->prox;
    }

    if (!erro) { //so entra se nao teve nenhum problema antes com os arquivos
        TNo *raiz = construirArvore(folhas, num_p); //constroi a arvore a partir das folhas
        while (raiz->vencedor->cod_cliente != INT_MAX) { //enquanto a raiz nao for HIGH VALUE...

            salva_cliente(raiz->vencedor, out); //salvando vencedor na saida
            TNo *folhaVencedora = raiz->endVencedor;
            free(folhaVencedora->vencedor);
            folhaVencedora->vencedor = NULL; //liberacao

            TCliente *proximo = le_cliente(folhaVencedora->f);
            if (proximo == NULL) {
                folhaVencedora->vencedor = cliente(INT_MAX, "");
            } else {
                folhaVencedora->vencedor = proximo; //novo vencedor
            }
            folhaVencedora->endVencedor = folhaVencedora;

            atualizarArvore(folhaVencedora);
        }
    }

    //para fechar arquivos e liberar folhas
    for (int i = 0; i < num_p; i++) {
        if (folhas[i]->f != NULL) {
            fclose(folhas[i]->f);
        }
        if (folhas[i]->vencedor != NULL) {
            free(folhas[i]->vencedor);
        }
        free(folhas[i]);
    }

    free(folhas);
    fclose(out);
}

void intercalacao_otima(char *nome_arquivo_saida, int num_p, Nomes *nome_particoes, int f) {

    char nomeNovaP[20]= "" ;
    Nomes *arquivo_atual = nome_particoes;//faz a leitura dos arquivos e conta quantos ainda tem
    Nomes *ultimo_arquivo = nome_particoes;//aponta pro ultimo arquivo para ser adicionado nova partição ao fim da lista
    while(ultimo_arquivo->prox != NULL )
        ultimo_arquivo = ultimo_arquivo->prox;//chega ate o ultimo arquivo
    int cont = 0; //para contar quantos arquivos tem
    int pcont = num_p; //apenas um auxiliar para nomear os novos arquivos

    while(arquivo_atual != NULL)// processa ate o arquivo ser nulo
    {
        while(cont != f-1 && arquivo_atual != NULL) // cont vai ate f-1 ou n ter mais arquivos pra ler
            {

                arquivo_atual = arquivo_atual->prox; // le e soma o cont
                cont++;
            }


        sprintf(nomeNovaP,"p%d.dat",++pcont); // apenas pra gerar um novo nome de arquivo

        if(arquivo_atual == NULL ) //se arquivo atual é nulo significa que nao tem menos arquivos que o maximo permitido entao pode ser ja gravado no arquivo final
            {
              intercalacao_basico(nome_arquivo_saida,cont,nome_particoes);

            }

        else //caso tenha mais arquivos que o maximo permitido :f-1{


        intercalacao_basico(nomeNovaP,cont,nome_particoes); //une os arquivos em um arquivo só usando itercalação basica e coloca ao fim da lista, usando a variavel ultimo_arquivo
        ultimo_arquivo->prox = cria_nomes(cria_str(nomeNovaP),NULL);

        ultimo_arquivo = ultimo_arquivo->prox ;



        for (int i = 0; i< cont; i++)
        nome_particoes = nome_particoes->prox; //avança a fila de nomes principal ja que os arquivos ja foram unidos no final
        }

        cont = 0; //reseta o cont
    }

}
