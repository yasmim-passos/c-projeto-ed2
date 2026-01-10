# 📂 Projeto Estrutura de Dados II - Intercalação de Partições Ordenadas em Arquivos (C)

Projeto desenvolvido em **linguagem C** para a disciplina **Estrutura de Dados II**, com foco em **Ordenação Externa de Arquivos**, abordando técnicas de **Intercalação Ótima** e **Árvore Binária de Vencedores**.

O trabalho foi implementado a partir de um **código base fornecido pelo professor**, seguindo rigorosamente as restrições do enunciado.

---

## 📌 Objetivo

Implementar os métodos de intercalação de partições ordenadas em arquivos binários, utilizando:

- **Intercalação Ótima**
- **Intercalação com Árvore Binária de Vencedores (ABV)**

Essas técnicas são utilizadas para combinar múltiplos arquivos previamente ordenados em um único arquivo final também ordenado, otimizando o número de acessos a disco.

---

## 🛠️ Funcionalidades Implementadas

Foram implementadas as seguintes funções no arquivo `intercalacao.c`:

```c
void intercalacao_otima(char *nome_arquivo_saida, int num_p, Nomes *nome_particoes, int f);

void intercalacao_arv_vencedores(char *nome_arquivo_saida, int num_p, Nomes *nome_particoes);
```

---

## ▶️ Como Executar

Este projeto utiliza o **código base fornecido pela disciplina**, que já contém o `main` responsável por chamar as funções de intercalação implementadas.

### Compilação

Utilize um compilador C (como `gcc`) para compilar todos os arquivos do projeto:

```bash
gcc *.c -o intercalacao

