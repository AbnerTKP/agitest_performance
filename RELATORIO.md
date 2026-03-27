# Relatório de Teste de Performance — BlazeDemo

## Objetivo

Validar se o site BlazeDemo (https://www.blazedemo.com/) atende ao critério de aceitação de 250 requisições por segundo com tempo de resposta no 90th percentil inferior a 2 segundos.

## Cenário

O teste simula o fluxo completo de compra de passagem aérea:

1. Acesso à página inicial
2. Busca de voos (Paris → Buenos Aires)
3. Seleção de voo
4. Preenchimento de dados do passageiro e confirmação da compra

Cada etapa valida se a resposta do servidor está de acordo com o esperado.

Foram executados dois cenários:

- **Teste de Carga:** 250 threads com ramp-up de 60 segundos e duração total de 180 segundos. Simula um crescimento gradual de usuários.
- **Teste de Pico:** 250 threads com ramp-up de 1 segundo e duração de 120 segundos. Simula um pico repentino de acessos.

## Resultados

### Teste de Carga

O teste de carga processou 7.744 requisições ao longo de 3 minutos, atingindo um throughput médio de aproximadamente 41 req/s. O tempo médio de resposta ficou em 2.621ms e o P90 em 8.153ms. A taxa de erro foi de 42,6%, sendo todos os erros do tipo HTTP 429 (Too Many Requests).

### Teste de Pico

O teste de pico processou 21.221 requisições em cerca de 3 minutos, com throughput médio de aproximadamente 110 req/s. O tempo médio de resposta foi de 2.312ms e o P90 ficou em 7.293ms. A taxa de erro foi de 40,0%, também exclusivamente HTTP 429.

## Conclusão

O critério de aceitação **não foi atendido**.

O throughput máximo alcançado foi de ~110 req/s no teste de pico, ficando abaixo da meta de 250 req/s. O P90 ficou em 7.293ms no melhor cenário, muito acima do limite de 2 segundos.

O principal fator limitante foi o rate limiting do servidor. Sob carga elevada, o BlazeDemo passa a rejeitar requisições com status 429 (Too Many Requests).

Além do rate limiting, as requisições que foram aceitas pelo servidor também apresentaram diminuição nos tempos de resposta conforme o número de usuários aumentava, o que indica que a aplicação não possui capacidade para escalar no volume configurado.

