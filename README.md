---
title: "Mapeamento — Subdivisão de Driverless"

team: "E-Racing Unicamp"

season: "2026"

authors: ["Arthur Morita e Gabriel De Marco"]

version: "1.0"

status: "draft"

created: "2026-07-24"

tags: ["driverless", "eracing", "unicamp", "semana_documentacao"]

---


# Mapeamento & Estado atual — Subdivisão de Driverless
## E-Racing Unicamp · Ciclo 2026

> Documentação referente ao período pós-onboarding redigido por novos membros para fins didáticos. Contém vídeo-aulas e conteúdos dinâmicos para aprendizado.

## Sumário

- [1. Primeiros Passos - Pré-Requisitos](#1-primeiros-passos)
- [2. O que é Mapeamento? - Introdução](#2-introducao)
- [3. Conceitos Base](#3-conceitos-base)
- [4. Objetivos - Cenário Atual](#4-objetivos)
- [5. Localização - Mapa Global](#5-slam)

## 1. Primeiros Passos - Pré Requisitos

### 1.1 Abertura
Seja bem vindo(a) à divisão de Driverless e, principalmente, à subdivisão de Mapeamento! Neste documento, iremos te guiar pelos seus primeiros passos como um membro efetivado da equipe. A partir daqui, você irá se situar e saberá o estado atual desta frente de Driverless. Porém, antes de começarmos a falar dos tópicos técnicos daqui, precisamos preparar nossa máquina para desenvolver projetos e nos certificar de que estamos prontos para rodar o que for necessário. Boa sorte! Aproveite!

### 1.2 Dependências técnicas
Enquanto membro de Mapeamento, faremos uso de algumas tecnologias típicas e imprescindíveis para o desenvolvimento de nossos projetos. Entre elas, algumas precisam de instalação separada ou cuidados especiais. 

**Tecnlogias típicas:** Python/C++, ROS2, Kalman Filter e Graph SLAM (ou similar).

Caso já tenha essas dependências instaladas, prossiga para o tópico 2.

### 1.3 Sistema Operacional
Para desenvolver seus projetos dentro desta subdivisão e como um membro de Driverless em geral, por favor, utilize Linux. 

Preferencialmente, caso não o possua em sua máquina, opte pela versão 22.04, que casa perfeitamente com a distribuição de ROS2 utilizada por nós. 

Em último caso, se já tiver uma versão Linux em sua máquina e ela esteja em outra versão, trate este problema com a utilização de contêineres através do Docker, uma plataforma que permite executar aplicações que possuem diferentes pré-requisitos (como versão de sistema operacional ou linguagem de programação) sem conflitos.

Recomendamos que, para manter seu sistema operacional de preferência (como o Windows), realize um dual boot em sua máquina. Abaixo, deixamos tutoriais para a instalação do Linux e a utilização do Docker para rodar o ROS2, que explicaremos neste documento.

**OBS**: No tutorial de Linux, certifique-se de baixar a versão 22.04 ao adentrar no site oficial do Ubuntu. Caso tenha problemas para realizar a partição, recomendamos a ferramenta MiniTool Partition Wizard, que resolve problemas de arquivos invisíveis ao particionar o disco.
> Tutorial Linux: https://www.youtube.com/watch?v=bzDDiH2Apac


> Tutorial Docker para ROS2: https://www.youtube.com/watch?v=oix-Qs75O08

### 1.4 Python/C++
Grande notícia! Na distribuição Ubuntu, Python já vem instalado nativamente. Inclusive, no Linux, muitas coisas são extremamente facilitadas quando o assunto é instalação de programas ou dependências. Para verificar a versão instalada, rode no terminal:

    python3 --version

Caso não tenha nenhuma base, aqui vai uma playlist introdutória com base de lógica de programação, orientação a objetos (muito importante para lidar com ROS2) e, no primeiro episódio, verificações iniciais do sistema em Linux.

> Playlist referenciada: https://www.youtube.com/watch?v=dzXGwmjpKPk&list=PLiLrXujC4CW3AFaMJyhbObGGlehxNC6BF&index=17 

Para C++, o assunto muda um pouco. Python é uma linguagem interpretada, o que significa que o código-fonte é transformado em algo executável e lido em tempo real. C++, por outro lado, é linguagem compilada, o que exige primeiramente que o código-fonte seja traduzido para linguagem de máquina para que depois torne-se executável. Para termos esse tradutor em nossa máquina, executemos os comandos a seguir:

    sudo apt update

    sudo apt install g++

O primeiro comando atualizaa lista de programas em nossa máquina. O segundo instala de fato o pacote principal do C++, incluindo co compilador. Pronto! Temos o básico para conseguir programar nossos sistemas.

### 1.5 ROS2
Agora, cuidaremos da instalacão do ROS2 em nossa máquina Linux na versão 22.04 da distribuição Ubuntu. Além disso, explicamos como funciona o ROS2 no contexto da nossa divisão de Driverless, especialmente em Mapeamento. Para conferir com mais clareza cada etapa, assista ao conteúdo abaixo:

> Parte 01 - Instalação do ROS2: (link a inserir - vídeo a produzir - roteiro do video: https://docs.google.com/document/d/1joCjW5q2515Jpfjb5NIcoxihvrFNfWErHdQmUgCS824/edit?tab=t.0)

**OBS**: Recomendamos que o vídeo abaixo seja visto após ler o resto do documento, para ter uma clareza do porque é importante o ROS2 aqui.
> Parte 02 - Funcionamento do ROS2 (link a inserir - vídeo a produzir)

## 2. O que é Mapeamento? - Introdução
Bom, agora temos tudo preparado para iniciarmos nossa imersão em Mapeamento.
Primeiramente, você deve entender qual o papel desta subdivisão no pipeline de atuação do carro autônomo. 

### 2.1 Visão Geral
Em Mapeamento, pegamos aquilo que a divisão de Percepção enxerga e transformamos em algo que a divisão de Controle consegue seguir. Tecnicamente, recebemos as coordenadas de detecção dos objetos demarcadores da pista (cones), construímos um mapa de informações e planejamos uma trajetória. A partir disso, Controle consegue calcular o esterçamento e torque necessários para seguir esta trajetória.

### 2.2 Arquitetura Atual
Para a comunicação entre os subsistemas de cada subdivisão a fim de construir o sistema como um todo, precisamos ter uma maneira de se comunicar para enviar: 

1. As coordenadas de Percepção para Mapeamento

2. A trajetória de Mapeamento para Controle

3. Os comandos de esterçamento e torque para o carro

4. As informações do carro para a Telemetria

Diante disso, utilizamos o middleware ROS2, que apesar do nome ser Robot Operating System, é um conjunto de ferramentas e bibliotecas que facilita o desenvolvimento de robôs e veículos autônomos através do protocolo de comunicação que ele possibilita. Nele, temos diferentes nós que publicam informações em tópicos e, a partir disso, realizam seus comandos e podem repassar informações. Agora provavelmente seja o momento ideal de você conferir aquele vídeo sobre ROS2 que passamos acima.

Como exemplo de funcionamento, podemos ter a seguinte estrutura:

    Nós:
    - perception_node
    - mapping_node
    - control_node

    Tópicos:
    - /coordinates
    - /waypoint

    Mensagens:
    - std_msgs/msg/Float32MultiArray

Exemplo:

    /coordinates:
    [x1, y1, x2, y2]

    /waypoint (ponto médio entre cones):
    [x_wp, y_wp]

O porquê deste waypoint ficará mais claro na seção de conceitos base, mas tenha em mente que é extremamente importante para a construção da trajetória. Normalmente, temos o eixo x como o que está na frente do carro e y como a lateral. Também é comum ter z como a distância à frente do veículo e x como a posição no eixo lateral.

Pipeline:
    Precepção -> Mapeamento -> Controle

    Telemetria (em Paralelo)

## 3. Conceitos Base
Diante da ideia apresentada, alguns conceitos base e ao mesmo tempo chave são imprescindíveis no que diz respeito a lidar com as informações recebidas de Percepção e construir a trajetória. Estes, por sua vez, são apresentados a seguir.

### 3.1 Referenciais: local e global
As coordenadas que chegam de Percepção sempre são tomadas em relação ao próprio carro, ou seja, há um referencial local no carro que caminha junto com ele e todas as coletas de pontos são passadas pela ótica deste referencial. Dessa forma, quando o sistema de Percepção detecta um cone, digamos, na posição (3,1), quer dizer que estamos assumindo o referencial do carro, portanto, corresponde a um cone a 3 metros a frente do carro e 1 metro a direita ou esquerda deste, dependendo da convenção entre positivo e negativo.

O grande problema de trabalhar em mapeamento com referencial local é que, ao longo do tempo, este referencial se altera, dado que o carro se movimenta. Com isso, para podermos gravar os cones já vistos, contruir um mapa consistente e de posições constantes da pista, além de acumular observações, trabalhamos com um referencial global.

Diante disso, você pode estar se perguntando: como escolher este referencial global? Quando o sistema inicia, o carro está numa posição. Esta posição é fixada como a posição inicial e fica imóvel durante o percurso do carro, tornando-se o ponto x = y = θ = 0. Caso você não tenha uma base de álgebra linear tão sólida, aqui vai um conteúdo sobre como relacionar estas coordenadas e realizar a transformação de coordenadas locais para coordenadas globais.

> Transformação de Coordenadas: (link a inserir - vídeo a produzir - basear-se no documento do drive MappingTutorial)

A ideia por trás é rotacionar o referencial do carro para se orientar da mesma maneira que o referencial global e, depois, transladar o ponto em questão para ser representado globalmente.

### 3.2 Classificação de cones
No contexto da Formula Student Driverless, em que nos baseamos em nosso sistema, os cones possuem diferentes cores e cada uma representa um lado da pista. Normalmente, são divididos entre cones azuis e amarelos, em que o primeiro normalmente representa o lado esquerdo da pista e o segundo o lado direito. Normalmente, há cones laranjas, que representam início ou fim de pista.

Diante disso, a divisão de Percepção fica encarregada de nos enviar os cones classificados quanto a sua classe/cor. Normalmente, podemos receber a cor através de uma estratégia chamada One Hot Encoding, em que cada cor representa uma configuração binária de valores. Poderíamos passar essas informações como uma simples string também, mas poderíamos arriscar um pouco a segurança por conta de strings serem trabalhosas de comparar em alguns casos, podendo apresentar diferenças de acentuação.

Exemplos de informações a receber de um cone detectado no referencial local em Percepção:
    cone = {"x": 5.2, "y": 1.4, "classe", "azul", "confidence": 0.87}

    cone = {"x": 5.2, "y": 1.4, [1, 0, 0], "azul", "confidence": 0.87}



No segundo caso, de One Hot Encoding, poderíamos ter, como exemplo, a seguinte identificação para cada cor:
    Azul: [1, 0, 0]
    Amarelo: [0, 1, 0]
    Laranja: [0, 0, 1]
Poderíamos também ter simplesmente 0 para azul e 1 para amarelo, diferenciando simplesmente esquerda e direita se a cor laranja não for de grande utilidade

### 3.3 Data Association
Com um referencial global em mãos, podemos associar novos dados obtidos a dados já constituintes do mapa, verificando se este novo dado é de fato um dado inusitado ou se ele é um dado já existente. Mesmo que o dado possua valores diferentes [ex: (10.1, 5.2) e (10.2, 5.3)], pequenas distorções podem ter ocorrido por conta de imprecisões de pista e/ou trajeto e ainda sim aquele dado corresponder ao mesmo cone.

Com isso, surge o processo de Data Association, que é um procedimento verificador de novos dados, o qual verifica se um novo cone é de fato um novo dado ou pode ser usado para atualizar um existente, sendo considerado diferente por fruto de ruído de sensores/pista. A ideia é simples: transformamos a detecção para o referencial global, calculamos a distância até cones já existentes e decidimos se é o mesmo cone ou um novo.

Se, para você, for mais fácil visualizar isso em código, aqui vai um exemplo simples da lógica por trás em Python:

    import math

    # Cones já existentes no mapa global
    mapa_global = [(10.1, 2.0), (5.3, -1.2),(7.8, 3.4)]

    # Nova detecção
    novo_cone = (10.3, 2.1)

    # Threshold é o limite de distância para considerar um dado como novo
    threshold = 0.5

    def distancia(p1, p2):

    return math.sqrt((p1[0] - p2[0])**2 + (p1[1] - p2[1])**2)

    cone_existente = False

    for cone in mapa_global:
        d = distancia(cone, novo_cone)
        if d < threshold:
        cone_existente = True
        print("Cone já existente no mapa")
        break

    if not cone_existente:
        mapa_global.append(novo_cone)
        print("Novo cone adicionado")
        
    print(mapa_global)

No exemplo, percorremos os cones armazenados, calculamos a distância até o novo dado e, a partir disso e do limiar (threshold), determinamos se devemos atualizar um cone existente ou criar um novo. Para análises mais avançadas, pode-se utilizar cor do cone, direção do carro e outros parâmetros para comparar dados novos com existentes.

Para encerrar esta parte, você talvez se pergunte: por qual motivo deveríamos gastar tempo verificando isso toda vez que um dado novo surge? Bom, sem a associação de dados (Data Association), podemos:
 - Carregar dados de cones duplicados
 - Acarretar numa pista inconsistente 
 - Consequentemente, gerar uma trajetória mal planejada para nossos amigos de Controle utilizarem como base de seus algoritmos.

### 3.4 Centerline
Agora, temos a base mínima para construir uma trajetória consistente. A partir daqui, vemos uma base de construção e, no próximo tópico, uma maneira mais eficiente e inteligente de construir um percurso.

A centerline, por si só, é a linha que liga todos os pontos médios entre cones detectados, formando uma trajetória a ser seguida. De maneira simples e considerando o que vimos nos tópicos anteriores, temos a seguinte ideia:

1. Achar um cone à esquerda
2. Achar um cone à direita
3. Calcular o ponto médio
4. Usar o ponto médio como waypoint

Para calcular o ponto médio, é tão simples quanto você está imaginando:
    Xm = (xe + xd)/2
    Ym = (ye + yd)/2
    Pm = (Xm, Ym)
Em que os subíndices correspondem a: m -> médio, e -> esquerda, d -> direita.

Este procedimento é conhecido como pareamento manual.

### 3.5 Triangulação de Delaunay
Tudo parecem flores, porém, temos uma má notícia: o método de pareamento manual, apesar de simples e eficiente, possui algumas limitações. O método depende de um pareamento correto entre o cone da esquerda e seu exato par à direita e, por isso, possui sensibilidade a cones faltando ou detectados incorretamente. Além disso, em pistas mais complexas, ele pode se complicar.

Porém, não se assuste! Temos uma solução. Atualmente, no cenário de Driverless, muitas equipes utilizam uma abordagem mais robusta para a construção da centerline: a Triangulação de Delaunay.

Neste método, pegamos todos os cones detectados como pontos e geramos uma malha triangular, isto é, ligamos todos os cones entre si de modo a formar vários e vários triângulos de associações entre eles. Diante disso, filtramos para sobrar apenas arestas que correspondam a ligações entre cones de lados opostos. A partir disso, o caminho se simplifica bastante: basta calcular os pontos médios dessas arestas e, ligando eles, teremos a trajetória a ser seguida de maneira mais estável.

Por qual motivo a trajetória é mais estável? Como ela depende somente de associar cones opostos sem que necessariamente um seja par de lado do outro, temos um algoritmo mais estável, reduzindo erros de pareamento mesmo que a trajetória esteja incompleta em alguns casos. Para entender melhor como esse cenário funciona visualmente, confira o conteúdo abaixo:

> Triangulação de Delaunay: (link a inserir: vídeo a produzir - utilizar o código no vídeo para exemplificar)

## 4. Objetivos - Cenário Atual
No conexto atual de Mapeamento, possuímos algumas limitações. Atualmente, operamos com mapeamento local, o que nos prejudica em pontos de planejamento de trajetória, citados e explicados anteriormente. Também, buscamos implementar a Triangulação de Delaunay, uma vez que hoje o sistema de planejamento de rota utiliza o pareamento manual.

No ciclo atual, temos como principal objetivo implementar o SLAM, acrônimo para Simultaneous Localization and Mapping, que, resumidamente, detecta continuamente a posição atual do carro no referencial global, permitindo reduzir erros de sensores e confirmar a posição de cones nesse referencial. Com o SLAM, conseguimos dar um salto imenso de qualidade para salvar observações e construir um trajeto consistente durante a volta exploratória das competições. A partir disso, o carro poderia completar voltas mais rapidamente com um trajeto precis já em mãos (ou em rodas, se preferir).

Além disso, nosso objetivo é testar e validar todo o sistema de Driverless com o carro em pista, uma vez que o sistema end-to-end já funciona em simulação. Com o SLAM, conseguimos masterizar a área de Mapeamento e planejamento de rota, visando competições internacionais e ser a primeira equipe da América Latina a percorrer com um sistema 100% autônomo.

## 5. Localização - Mapa Global

### 5.1 SLAM - Simultaneous Localization and Mapping

### 5.2 Filtro de Kalman

## Referências
 - https://gitlab.com/unicamperacing/autonomous-systems/driverless/quantum/mapping/path-planning 
 - https://drive.google.com/file/d/1j4W2X5R0DTC9kRmWkYO2WTCVTk08xBV2/view
 - https://www.youtube.com/playlist?list=PLn8PRpmsu08pzi6EMiYnR-076Mh-q3tWr