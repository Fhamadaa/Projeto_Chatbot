# Projeto_Chatbot

O presente projeto foi elaborado com o intuito de operar como um chatbot. Este, nada mais é do que uma forma de interação com usuários através de conversas automatizadas. Uma utilização comum do chatbot, seria no atendimento ao cliente, onde este consegue esclarecer dúvidas e resolver problemas, sem a necessidade de um atendimento humanizado.

O telegram, que por sua vez permite a implementação da funcionalidade de criação de chatbots, foi a plataforma de mensagens escolhido para utilização dessa automação. Uma vez definido este sistema transacional, foi elaborado o sistema analítico, onde os dados produzidos foram analisados. Este foi segregado em 3 partes: ingestão, ETL e apresentação. Todo sistema analítico foi construído na AWS (Amazon Web Services) e toda essa arquitetura esta representada na imagem abaixo:

![Profissao Analista de dados M42 Material de apoio arch](https://github.com/user-attachments/assets/27f3b946-f45c-44ec-9e63-8d8c180ae72a)

Dentro da plataforma da AWS, foram elaboradas as seguintes etapas:

**1.Ingestão de dados**

Primeiramente foram criados os buckets, um para os dados originais que foram coletados sem nenhum tratamento e outro para os dados devidamente tratados, que será utilizado na parte de ETL. Na sequência, foi criada uma função Lambda na qual ela deve receber as mensagens, verificar se a sua origem corresponde ao local certo e persistir as mensagens no formato JSON para o bucket dos dados originais. 
Com os buckets criados e a primeira função Lambda pronta, foi criado uma API dentro do AWS API GATEWAY, na qual esta tem a função de receber as mensagens pelo bot do Telegram, enviadas via webhook e iniciar uma função Lambda, passando o conteúdo da mensagem para o seu parâmentro.

**2.ETL (Extração, Transformação e Carregamento)**

Nesta etapa os dados passam por um processo recorrente de data wrangling onde o dado é limpo, parcionado e pronto para ser analisado. No projeto, as mensagens de um único dia, já persistidas no processo de ingestão, serão compactas em um único arquivo, orientado a coluna e comprimido, que será persistido em uma camada enriquecida. Para isso, foi utilizado outra função Lambda e o outro bucket criado na etapa anterior, onde os dados processados pela função serão armazenados. A função tem como objetivo listar todos os arquivos JSON de uma única partição do bucket com os dados originais, onde para cada arquivo listado, faz o download do arquivo, executa uma função wrangling (disposta no final do código) e cria uma tabela do PyArrow contatenando com as demais.

Por fim, o AWS Event Brigde opera a função de agendador, onde este ativa diariamente a função do ETL.

**3.Apresentação**

Por fim, nesta etapa os dados devem ser entregues para os usuários e sistemas através de uma interface de fácil utilização, como o SQL. Logo, esta estapa será a única em que a maioria dos usuários terão acesso. Foi levado em consideração a eficiência para entrega dos dados armazenados, onde estes utilizaram camadas mais refinadas para produzir consultas mais baratas e dados mais consistentes.

Aqui foram analisados fatores de interesse, levando em consideração os dados enviados e coletados pelo chatbot como:

- Quantidade de mensagens enviadas por dia
- Quantidade de mensagens enviadas por usuário por dia
- Tamanho das mensagens por usuário por dia
- Quantidade de mensagens por hora e por dia da semana
  
Nesta etapa de apresentação, foi utilizado o serviço AWS Athena. Este por sua vez, importa os dados processados e armazenados no devido bucket e possibilita a criação de uma tabela para consulta analítica utilizando o SQL para extração de insights.

Uma vez criada a tabela para consultas, foram realizadas as análises propostas

--> Levantamento da quantidade de mensagens enviadas por dia
![Athena - Consulta de msg por dia](https://github.com/user-attachments/assets/c3b59317-2665-46a8-b02f-c9336aa01c98)

--> Levantamento da quantidade de mensagens enviadas por dia por usuário
![Athena - Consulta de msg por usuário por dia](https://github.com/user-attachments/assets/38180871-1069-4e10-8cc2-8af868ae1ab5)

--> Levantamento do tamanho das mensagens por usuário por dia
![Athena - Consulta de tamanho de msg por usuário por dia](https://github.com/user-attachments/assets/f3404534-e856-4956-a723-adda9f58f626)

--> Levantamento da quantidade de mensagens por hora e por dia da semana
![Athena - Consulta de msg por hora e por dia da semana](https://github.com/user-attachments/assets/3690de9d-1595-4dbe-b905-aef97a2a9203)





