## Laboratório: Trabalhando com Machine Learning na Prática no Azure ML

Este repositório tem como objetivo entregar o projeto final do módulo "Trabalhando com Machine Learning na Prática no Azure ML" do Bootcamp "Microsoft Azure AI Fundamentals" da DIO, ministrado pela Valéria Baptista.

O desenvolvimento deste projeto visa orientar a criação de um modelo de Machine Learning no Microsoft Azure ML, com base nas orientações contidas no site da [Microsoft Learn](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/01-machine-learning.html).

Neste exercício, você usará o recurso de aprendizado de máquina automatizado no Azure Machine Learning para treinar e avaliar um modelo de aprendizado de máquina. Em seguida, você implantará e testará o modelo treinado.

### Criando um espaço de trabalho do Azure Machine Learning

Para usar o Azure Machine Learning, você precisa provisionar um espaço de trabalho do Azure Machine Learning em sua conta do Azure. Então você poderá usar o Azure Machine Learning Studio para trabalhar com os recursos em seu espaço de trabalho.

    Dica: Se você já tem um espaço de trabalho do Azure Machine Learning, pode usá-lo e pular para a próxima tarefa.


1. Faça login no portal do [Azure](https://portal.azure.com) usando suas credenciais da Microsoft.
2. Selecione + Criar um recurso, pesquise por Machine Learning e crie um novo recurso do Azure Machine Learning com as seguintes configurações:

    >**Assinatura**: Sua assinatura do Azure.

    >**Grupo de recursos**: Crie ou selecione um grupo de recursos.

    >**Nome**: Insira um nome único para o seu espaço de trabalho.

    >**Região**: Selecione a região geográfica mais próxima.

    >**Conta de armazenamento**: Observe a nova conta de armazenamento padrão que será criada para o seu espaço de trabalho.

    >**Cofre de chaves**: Observe o novo cofre de chaves padrão que será criado para o seu espaço de trabalho.

    >**Insights de aplicação**: Observe o novo recurso de insights de aplicação padrão que será criado para o seu espaço de trabalho.

    >**Registro de contêiner**: Nenhum (um será criado automaticamente na primeira vez que você implantar um modelo em um contêiner).


3. Selecione **Revisar + criar**, depois selecione **Criar**. Aguarde a criação do seu espaço de trabalho (pode levar alguns minutos) e, em seguida, vá para o recurso implantado.

4. Selecione **Iniciar estúdio** (ou abra uma nova guia do navegador e navegue até https://ml.azure.com, e faça login no Azure Machine Learning Studio usando sua conta Microsoft). Feche todas as mensagens que são exibidas.

5. No Azure Machine Learning Studio, você deve ver o espaço de trabalho recém-criado. Se não, selecione **Todos os espaços de trabalho** no menu à esquerda e, em seguida, selecione o espaço de trabalho que você acabou de criar.

### Use o aprendizado de máquina automatizado para treinar um modelo

O aprendizado de máquina automatizado permite que você experimente vários algoritmos e parâmetros para treinar vários modelos e identificar o melhor para seus dados. Neste exercício, você usará um conjunto de dados de detalhes históricos de aluguel de bicicletas para treinar um modelo que prevê o número de aluguéis de bicicletas que devem ser esperados em um determinado dia, com base em características sazonais e meteorológicas.

	Citação: Os dados usados neste exercício são derivados do Capital Bikeshare e são usados de acordo com o acordo de licença de dados publicado.

1. No Azure Machine Learning Studio, visualize a página **ML Automatizado** (em **Criação**).
2. Crie um novo trabalho ML Automatizado com as seguintes configurações, usando **Avançar** conforme necessário para avançar na interface do usuário:

	**Configurações básicas**:
	>**Nome do trabalho**: mslearn-bike-automl
	
    >**Novo nome de experimento**: mslearn-bike-rental
	
	>**Descrição**: Aprendizado de máquina automatizado para previsão de aluguel de bicicletas

	>**Tags**: *nenhuma*

    **Tipo de tarefa e dados**:

    - **Selecione o tipo de tarefa**: Regressão
    - **Selecionar conjunto de dados**: Crie um novo conjunto de dados com as seguintes configurações:

        **Tipo de dados**:

        - **Nome**: aluguel de bicicletas
        - **Descrição**: dados históricos de aluguel de bicicletas
        - **Tipo**: Tabular
        
        **Fonte de dados**:

        - Selecione **Dos arquivos da web**
        
        **URL da Web**:
             
        - **URL da web**: https://aka.ms/bike-rentals
        - **Ignorar validação de dados**: não selecionar
        
        **Configurações**:

        - **Formato de arquivo**: Delimitado
        - **Delimitador**: Vírgula
        - **Codificação**: UTF-8
        - **Cabeçalhos de coluna**: apenas o primeiro arquivo possui cabeçalhos
        - **Pular linhas**: nenhuma
        - **O conjunto de dados contém dados multilinhas**: *não selecione*
        
        **Esquema**:

        - Incluir todas as colunas exceto **Caminho**
        - Revise os tipos detectados automaticamente

    Selecione **Criar**. Após a criação do conjunto de dados, selecione o conjunto de dados **bike-rentals** para continuar a enviar o trabalho de ML Automatizado.

**Configurações de tarefa**:

- **Tipo de tarefa**: Regressão
- **Conjunto de dados**: aluguel de bicicletas
- **Coluna de destino**: Aluguéis (inteiro)
- **Configurações adicionais**:
    - **Métrica primária**: erro quadrático médio normalizado
    - **Explique o melhor modelo**: *Não selecionado*
    - **Usar todos os modelos suportados**: *desmarcado*. Você restringirá o trabalho para tentar apenas alguns algoritmos específicos.
    - **Modelos permitidos**: Selecione apenas **RandomForest** e **LightGBM** - normalmente você gostaria de tentar o máximo possível, mas cada modelo adicionado aumenta o tempo necessário para executar o trabalho.

- **Limites**: *expanda esta seção*
    - **Máximo de testes**: 3
    - **Máximo de testes simultâneos**: 3
    - **Máximo de nós**: 3
    - **Limite de pontuação da métrica:** 0,085 (*de modo que, se um modelo atingir uma pontuação da métrica de erro quadrático médio normalizado de 0,085 ou menos, o trabalho será encerrado.*)
    - **Tempo limite**: 15
    - **Tempo limite de iteração**: 15
    - **Habilitar rescisão antecipada**: selecionado
    - **Validação e teste**:
    - **Tipo de validação**: divisão de validação de trem
    - **Porcentagem de dados de validação**: 10
    - **Conjunto de dados de teste**: *Nenhum*

**Calcular**:
- **Selecione o tipo de computação**: sem servidor
- **Tipo de máquina virtual**: CPU
- **Camada de máquina virtual**: Dedicada
- **Tamanho da máquina virtual**: Standard_DS3_V2*
- **Número de instâncias**: 1

    * *Se a sua assinatura restringir os tamanhos de VM disponíveis para você, escolha qualquer tamanho disponível*.

3. Envie o trabalho de treinamento. Ele inicia automaticamente.

4. Espere o trabalho terminar. Pode demorar um pouco – agora pode ser um bom momento para uma pausa para o café!

### Avalie o melhor modelo

Quando o trabalho automatizado de aprendizado de máquina for concluído, você poderá revisar o melhor modelo treinado.

1. Na guia **Visão geral** do trabalho automatizado de aprendizado de máquina, observe o melhor resumo do modelo.
2. Selecione o texto em **Nome do algoritmo** do melhor modelo para visualizar seus detalhes.

3. Selecione a guia **Métricas** e selecione os gráficos **residuais** e **predito_true**, se eles ainda não estiverem selecionados.

     Revise os gráficos que mostram o desempenho do modelo. O gráfico de **resíduos** mostra os resíduos (as diferenças entre os valores previstos e reais) como um histograma. O gráfico predito_true compara os valores previstos com os valores verdadeiros.

### Implantar e testar o modelo

1. Na guia **Modelo** do melhor modelo treinado pelo seu trabalho automatizado de machine learning, selecione **Implantar** e use a opção de **Serviço Web** para implantar o modelo com as seguintes configurações:
	- **Nome**: prever-aluguéis
	- **Descrição**: Prever aluguel de bicicletas
	- **Tipo de computação**: Instância de Contêiner do Azure
	- **Habilitar autenticação**: *selecionado*
2. Aguarde o início da implantação – isso pode levar alguns segundos. O **status de implantação** do ponto de extremidade **predict-rentals** será indicado na parte principal da página como *Em execução*.
3. Aguarde até que o **Status da implantação** mude para *Bem-sucedido*. Isso pode levar de 5 a 10 minutos.

### Testar o serviço implantado

Agora você pode testar seu serviço implantado.

1. No estúdio Azure Machine Learning, no menu esquerdo, selecione **Pontos de Extremidades** e abra o ponto de extremidade em tempo real **predict-rentals**.
2. Na página do ponto de extremidade em tempo real **predict-rentals**, visualize a guia **Teste**.
3. No painel **Dados de entrada para testar o ponto de extremidade**, substitua o modelo JSON pelos seguintes dados de entrada:

```json
   {
   "Inputs": { 
     "data": [
       {
         "day": 1,
         "mnth": 1,   
         "year": 2022,
         "season": 2,
         "holiday": 0,
         "weekday": 1,
         "workingday": 1,
         "weathersit": 2, 
         "temp": 0.3, 
         "atemp": 0.3,
         "hum": 0.3,
         "windspeed": 0.3 
       }
     ]    
   },   
   "GlobalParameters": 1.0
 }

```

4. Clique no botão **Testar**.
5. Revise os resultados do teste, que incluem um número previsto de aluguéis com base nos recursos de entrada - semelhante a este:

```json
 {
   "Results": [
     444.27799000000000
   ]
 }
```

O painel de teste pegou os dados de entrada e usou o modelo treinado para retornar o número previsto de aluguéis.

Vamos revisar o que você fez. Você usou um conjunto de dados históricos de aluguel de bicicletas para treinar um modelo. O modelo prevê o número de aluguéis de bicicletas esperados num determinado dia, com base em características sazonais e meteorológicas.

## Arquivo para teste do ponto de extremidade

O ponto de extremidade pode ser testado com o arquivo [endpoint.json](https://github.com/eguedes/bootcampDIO_lab-endpoints/blob/main/endpoint.json).


## Limpando

O serviço web que você criou está hospedado em uma instância de contêiner do Azure. Se você não pretende fazer mais experiências com ele, exclua o ponto de extremidade para evitar o acúmulo desnecessário de uso do Azure.

1. No estúdio Azure Machine Learning, na aba **Pontos de extremidade**, selecione o ponto de extremidade **predict-rentals**. Em seguida, selecione **Excluir** e confirme que deseja excluir o ponto de extremidade.

     Excluir sua computação garante que sua assinatura não será cobrada por recursos de computação. No entanto, será cobrada uma pequena quantia pelo armazenamento de dados, desde que o espaço de trabalho do Azure Machine Learning exista na sua assinatura. Se tiver terminado de explorar o Azure Machine Learning, poderá apagar o espaço de trabalho Azure Machine Learning e os recursos associados.

Para excluir seu espaço de trabalho:

1. No portal Azure, na página **Grupos de recursos**, abra o grupo de recursos especificado ao criar o seu espaço de trabalho Azure Machine Learning.
2. Clique em **Excluir grupo de recursos**, digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir**.
