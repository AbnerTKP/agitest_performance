# BlazeDemo - Teste de Performance

Projeto de teste de performance para o site [BlazeDemo](https://www.blazedemo.com/) usando Apache JMeter, simulando o fluxo completo de compra de passagem aérea.

## Pré-requisitos

Antes de rodar o projeto, é necessário ter instalado o **Java 8+** e o **Apache JMeter 5.x**. Abaixo estão as instruções de instalação para cada sistema operacional.

### Windows

1. Baixe e instale o JDK 8+ em https://adoptium.net/ (marque a opção de configurar o JAVA_HOME durante a instalação).
2. Baixe o JMeter em https://jmeter.apache.org/download_jmeter.cgi (arquivo .zip dos Binaries).
3. Extraia o zip em uma pasta, por exemplo `C:\jmeter`.
4. Abra um terminal e verifique:
   ```bash
   java -version
   ```

### macOS

1. Instale o Java usando o [Homebrew](https://brew.sh/):
   ```bash
   brew install openjdk@17
   ```
2. Baixe e extraia o JMeter:
   ```bash
   brew install jmeter
   ```
3. Verifique a instalação:
   ```bash
   java -version
   jmeter --version
   ```

### Linux (Ubuntu/Debian)

1. Instale o Java via apt:
   ```bash
   sudo apt update
   sudo apt install openjdk-17-jdk
   ```
2. Baixe e extraia o JMeter:
   ```bash
   wget https://archive.apache.org/dist/jmeter/binaries/apache-jmeter-5.6.3.zip
   unzip apache-jmeter-5.6.3.zip
   ```
3. Verifique a instalação:
   ```bash
   java -version
   ```

> Em todos os casos, `java -version` deve mostrar versão 8+. O JMeter é executado via `bin/jmeter.bat` (Windows) ou `bin/jmeter` (macOS/Linux).

## Passo a passo para rodar

1. Clone o repositório:
   ```bash
   git clone https://github.com/AbnerTKP/agitest_performance.git
   ```

2. Acesse a pasta do projeto:
   ```bash
   cd agitest_performance
   ```

3. Certifique-se de que o JMeter está no PATH do sistema. Para verificar:
   ```bash
   jmeter --version
   ```
   Se o comando não for reconhecido, adicione a pasta `bin` do JMeter ao PATH do sistema:
   - **Windows:** Pesquise "Variáveis de Ambiente" no menu Iniciar, edite a variável `Path` e adicione o caminho completo da pasta `bin` do JMeter (ex: `C:\apache-jmeter-5.6.3\bin`)
   - **macOS/Linux:** Adicione ao `~/.bashrc` ou `~/.zshrc`:
     ```bash
     export PATH=$PATH:/caminho/para/apache-jmeter-5.6.3/bin
     ```

4. Execute o **Teste de Carga**:
   ```bash
   jmeter -n -t blazedemo-teste-carga.jmx -l resultado-carga.jtl -e -o relatorio-carga
   ```

5. Execute o **Teste de Pico**:
   ```bash
   jmeter -n -t blazedemo-teste-pico.jmx -l resultado-pico.jtl -e -o relatorio-pico
   ```

6. Abra os relatórios HTML no navegador:
   - Teste de Carga: `relatorio-carga/index.html`
   - Teste de Pico: `relatorio-pico/index.html`

7. Para rodar novamente, apague os arquivos gerados antes de executar:
   ```bash
   rm -rf resultado-carga.jtl relatorio-carga
   rm -rf resultado-pico.jtl relatorio-pico
   ```

## O que é testado

O script simula o fluxo completo de compra de passagem aérea, com validações em cada etapa.

**01 - Página Inicial** — Acessa a home do BlazeDemo
- Valida a presença do texto "Welcome to the Simple Travel Agency!"

**02 - Buscar Voos** — Busca voos de Paris para Buenos Aires
- Envia formulário com origem e destino
- Valida a presença do texto "Flights from Paris to Buenos Aires"

**03 - Selecionar Voo** — Seleciona o primeiro voo disponível
- Envia dados do voo selecionado (companhia, preço)
- Valida a presença do texto "Your flight from TLV to SFO has been reserved."

**04 - Confirmar Compra** — Preenche dados do passageiro e finaliza
- Envia nome, endereço, dados do cartão
- Valida a mensagem de sucesso: "Thank you for your purchase today!"

## Cenários de teste

| Cenário | Arquivo | Threads | Ramp-up | Duração | Descrição |
|---|---|---|---|---|---|
| **Teste de Carga** | `blazedemo-teste-carga.jmx` | 250 | 60s | 180s | Ramp-up gradual de 0 a 250 usuários em 60s, mantém por mais 120s |
| **Teste de Pico** | `blazedemo-teste-pico.jmx` | 250 | 1s | 120s | Pico de 250 usuários simultâneos em 1 segundo |

**Critério de aceitação:** 250 requisições/segundo com tempo de resposta no 90th percentil inferior a 2 segundos.

## Resultados

Os resultados e a análise completa estão no [Relatório de Teste de Performance](RELATORIO.md).

## CI

O projeto inclui um workflow do GitHub Actions que executa automaticamente os testes de carga e de pico a cada push ou pull request na branch `main`. Os relatórios HTML são salvos como artifacts do workflow.

Para executar manualmente: vá na aba **Actions** do repositório e clique em **Run workflow**.

## Estrutura do projeto

```
agitest_performance/
├── .github/
│   └── workflows/
│       └── performance-test.yml   # Pipeline CI
├── blazedemo-teste-carga.jmx      # Script JMeter - cenário de carga
├── blazedemo-teste-pico.jmx       # Script JMeter - cenário de pico
├── RELATORIO.md                   # Relatório de análise dos resultados
├── .gitignore                     # Ignora arquivos gerados (.jtl, relatórios)
└── README.md                      # Este arquivo
```

## Tecnologias

- **Apache JMeter 5.6.3** — Ferramenta de teste de performance
- **Java 8+** — Runtime necessário para o JMeter
- **GitHub Actions** — Integração contínua

## Autor

**Abner Souza**
QA
[LinkedIn](https://linkedin.com/in/abnersouza1)
