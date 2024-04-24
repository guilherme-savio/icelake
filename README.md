<div align="center">
  <h3 align="center">Icelake</h3>

  <p align="center">
    Demonstração e prática de Apache Spark com Delta Lake e Apache Iceberg para a disciplina de Engenharia de Dados
    <br />
    <strong>Desenvolvido por: </strong>
    <br />
    <a href="https://github.com/guilherme-savio">Guilherme Savio</a>
    ·
    <a href="https://github.com/higorgoulart">Higor Goulart</a>
    ·
    <a href="https://github.com/bruno-venturini">Bruno Venturini</a>
  </p>
</div>


## Sobre o projeto

O projeto cria aplicações conteinerizadas contendo implementações de Apache Spark+Delta Lake, Apache Spark+Apache Iceberg, bucket via Min.IO, uma base de dados contento informações sobre mudanças climáticas na terra com registros de temperatura e notebooks com manipulações de cada ambiente.

## Referência

** A base de dados utilizada neste projeto pode ser encontrada neste <a href="https://www.kaggle.com/datasets/berkeleyearth/climate-change-earth-surface-temperature-data">link</a>. Não houveram modificações no conjunto de dados, apenas conversão do formato .CSV para PARQUET devido limitações de armazenamento do GitHub.
<br />

** Os arquivos do tipo `Dockerfile` e `docker-compose.yml` são de origem do guia de uso encontrado na documentação do (<a href="https://iceberg.apache.org/spark-quickstart/#spark-and-iceberg-quickstart">Apache Iceberg</a>). Para a construção e execução deste projeto, foram necessárias modificações nos arquivos base que podem ser encontrados <a href="https://github.com/tabular-io/docker-spark-iceberg">aqui</a>. A modificação realizada não é para uso comercial. Todos os direitos de desenvolvimento pertencem à <a href="https://tabular.io/">Tabular</a>.

## Construído com

<div >
	<code><img width="50" src="https://user-images.githubusercontent.com/25181517/183914128-3fc88b4a-4ac1-40e6-9443-9a30182379b7.png" alt="Jupyter Notebook" title="Jupyter Notebook"/></code>
	<code><img width="50" src="https://user-images.githubusercontent.com/25181517/183423507-c056a6f9-1ba8-4312-a350-19bcbc5a8697.png" alt="Python" title="Python"/></code>
	<code><img width="50" src="https://user-images.githubusercontent.com/25181517/117207330-263ba280-adf4-11eb-9b97-0ac5b40bc3be.png" alt="Docker" title="Docker"/></code>
	<code><img width="50" src="https://user-images.githubusercontent.com/25181517/184357834-eba1eee1-6074-4b9c-8ed3-5373868096cc.png" alt="Apache Spark" title="Apache Spark"/></code>
</div>

## Iniciando

Nesta seção você encontrará os passos para instalar e executar o projeto na sua máquina.

## Pré-requisitos

* <a href="https://www.docker.com/products/docker-desktop/">Docker Desktop</a>

## Instalação

1. Clone o repositório:
   ```sh
   git clone https://github.com/higorgoulart/bicho.bet.git
   ```
2. Com o Docker Desktop instalado, garanta que ele esteja em execução para o funcionamento correto do docker-cli.

3. Dentro da pasta raiz do projeto, execute o comando abaixo. Ele realizará o download das imagens e a configuração do ambiente de maneira automática.
   ```sh
   docker-compose up
   ```
4. Após a execução do comando anterior, você pode checar os serviços criados nas seguintes portas:
<table>
  <tr>
    <th>Link</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td><a href="http://localhost:8888">http://localhost:8888</a></td>
    <td>Jupyter conectado ao ambiente Apache Iceberg</td>
  </tr>
  <tr>
    <td><a href="http://localhost:8881">http://localhost:8881</a></td>
    <td>Jupyter conectado ao ambiente Delta Lake</td>
  </tr>
  <tr>
    <td><a href="http://localhost:9001">http://localhost:9001</a></td>
    <td>Painel administrativo do Min.IO. O usuário e senha são respectivamente `admin` e `password`</td>
  </tr>
</table>

## Análises e Resultados



