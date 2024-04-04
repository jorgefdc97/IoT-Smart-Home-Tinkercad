# IoT-Smart-Home-Tinkercad
Smart Home assistant simulated in Tinkercad

Project link: https://www.tinkercad.com/things/6C1RrYInlZC-projeto-iot-20232024-445494728942906?sharecode=ftWysVo9xSIrVLEyymCQkkw1OdeFRVwK8jb7SAFR6sI

PT-pt

ÍNDICE

    IMPLEMENTAÇÃO DO PROJETO
    FUNCIONALIDADES
        Visor LCD
        Deteção e alarme de gás
        Deteção e controlo de temperatura
        Deteção e alarme de incêndio
        Ajuste de iluminação automático
        Abertura automática do portão
        Teclado com código de segurança
        Botão para abrir/fechar porta de entrada
        Controlo remoto infravermelhos
            Introdução de código de segurança
            Visualização das temperaturas interior e exterior
            Alteração da frequência (Hz) do alarme

ANEXOS
    Anexo I - Tabela_I_Lista_Componentes.csv
    Anexo II - Figura_I_Vista_Circuito.png
    Anexo III - Figura_II_Vista_Esquematica.pdf
    Anexo IV - Figura_III_LCD.png
    Anexo V - ArduinoInterior
    Anexo VI - ArduinoExterior
    Anexo VII - Mapeamento_Controlo_Remoto.png
    Anexo VIII - Comandos_Arduino_Interior.png


IMPLEMENTAÇÃO DO PROJETO

    Nesta secção, serão apresentadas as motivações do projeto, os componentes utilizados (Tabela_I_Lista_Componentes), a vista do circuito desenvolvido (Tabela_I_Lista_Componentes) e a vista esquemática do circuito desenvolvido (Figura_II_Vista_Esquematica). 

    O objetivo do grupo, ao longo do projeto, foi explorar e experimentar os vários componentes que o simulador Tinkercad dispõe para desenvolver um assistente que poderia ser implementado numa casa, na realidade. Para além da curiosidade em relação às funcionalidades dos componentes individualmente, também foi explorada a interligação entre os vários componentes e as funcionalidades que poderiam ser extraídas caso houvesse conexões e transferências de informação. Por esse motivo, não foram utilizados componentes em duplicado, a menos que a sua funcionalidade provoque e/ou sofra comportamentos diferentes daqueles já obtidos.
    O assistente de casa inteligente desenvolvido (Figura_I_Vista_Circuito) simula 2 ambientes (interior e exterior) de uma casa, conectados pelos Arduinos: ambiente interior representado na parte inferior da Figura_I_Vista_Circuito; ambiente exterior representado na parte superior da Figura_I_Vista_Circuito.

    FUNCIONALIDADES
    
        VISOR LCD
            Objetivo: Transmitir, ao utilizador, mensagens de informação do sistema
            Componentes utilizados: U_INT_LCD
            Implementação: As mensagens apresentadas aos utilizadores variam consoante o estado do assistente, cujas alterações serão descritas abaixo. O LCD, entre outras mensagens, apresentará 3 estados: OK, GAS, FIRE

        DETEÇÃO E ALARME DE GÁS
            Objetivo: Sinalizar a deteção de gás
            Componentes utilizados: 
                GAS_INT_GasSensor
                PIEZO_INT_Alarm
                U_INT_LCD
            Implementação: No caso do sensor de gás detetar um valor de 100 ppm de concentração de gás no ambiente, fará o alarme disparar e uma mensagem no LCD irá aparecer com “GAS”. O valor de 100 p.p.m. (partes por milhão) foi escolhido por ser o valor de concentração de gás que começa a ser prejudicial para a saúde (apesar de ser por longa exposição).

        DETEÇÃO E CONTROLO DE TEMPERATURA
            Objetivo: Detetar alterações de temperatura e ligar/desligar ar condicionado
            Componentes utilizados: 
                U_INT_TemperatureSensor
                D_INT_GreenLed
                D_INT_RedLed
                D_INT_BlueLed
                U_INT_LCD
            Implementação: Através do sinal capturado pelo sensor de temperatura (U_INT_TemperatureSensor), que é transformado em graus Celsius (Equação 1), é verificada a temperatura e, em caso de:
                Temperatura acima dos 27ºC: é transmitida uma mensagem no LCD – “Turning on AC” – e o LED vermelho (D_INT_RedLed) acende;
                Temperatura entre os 27ºC e os 21ºC: acende o LED verde (D_INT_GreenLed) e mantém o ar condicionado na situação prévia (ligado ou desligado);
                Temperatura abaixo dos 21ºC: é transmitida uma mensagem no LCD – “Turning off AC” – e o LED azul (D_INT_BlueLed) acende.

            Equação 1 – Cálculo da temperatura (em Celsius) capturada pelo sensor de temperatura
                
                input=(analogRead(SENSOR_TEMP)  *5000.0)/1024.0
                celsius=  (input-500.0)/10.0

                analogRead: função embutida no Arduino para ler a frequência recebida
                SENSOR_TEMP: identificação do PIN onde o sensor de temperatura está ligado
            
        DETEÇÃO E ALARME DE INCÊNDIO
            Objetivo: Sinalizar a deteção de fogo 
            Componentes utilizados: 
                GAS_INT_GasSensor
                U_INT_TemperatureSensor
                PIEZO_INT_Alarm
                U_INT_LCD
            Implementação: Devido ao simulador Tinkercad não ter um detetor de fumo, foi feita uma combinação entre detetor de gás (GAS_INT_GasSensor) e sensor de temperatura (U_INT_TemperatureSensor), ou seja, caso exista uma concentração de gás de 100 p.p.m. (partes por milhão) e uma temperatura acima dos 70ºC, o alarme (PIEZO_INT_Alarm) dispara e é transmitida a mensagem “FIRE” no LCD (U_INT_LCD). A temperatura de indicação de incêndio foi escolhida devido à temperatura inicial que um foco de incêndio produz, enquanto não interfere com elevadas temperaturas ambientais.

        AJUSTE DE ILUMINAÇÃO AUTOMÁTICO
            Objetivo: Acender a luz através de sensor de movimento em caso de pouca luminosidade
            Componentes utilizados: 
                L_INT_Lights
                K_INT_Rele
                PIR_INT_MovementSensor
                R_INT_LightSensor
            Implementação: Sempre que for detetado movimento pelo sensor (PIR_INT_MovementSensor), é verificado o nível de luz lido, analogicamente, pelo fotorresistor (R_INT_LightSensor). Caso o nível de luz seja considerado baixo, a luz acende. Caso o nível de luz seja considerado alto ou razoável, mesmo que seja detetado movimento, a luz não se acenderá. Optou-se por utilizar um relé (K_INT_Rele) porque, apesar de a lâmpada (L_INT_Lights) funcionar no simulador, em ambiente real, é necessária a transformação de corrente visto que, a potência transmitida pelo Arduino é de 5V e a lâmpada utilizada funciona a partir dos 120V.
            Nota: Apesar de, ser pedido, no enunciado, que fosse transmitida uma mensagem a indicar a mudança do estado da luz, optou-se por não o fazer devido à importância de outro tipo de mensagens, já que o estado das luzes é observado pelo próprio acender das luzes.

        ABERTURA AUTOMÁTICA DO PORTÃO
            Objetivo: Abrir portão de entrada com aproximação do carro.
            Componentes utilizados: 
                DIST_EXT_GateDistanceSensor
                SERVO_EXT_Gate
            Implementação: Através do sensor ultrassom de distância (DIST_EXT_GateDistanceSensor), é possível medir a distância do carro e do sensor instalado no portão através do tempo do eco (Equação 2). 
            Nota: Como, neste caso, está a ser desenvolvida uma simulação, não foi implementado o RFID (Identificação por Frequência de Rádio, do inglês, Radio Frequency Identification) que serviria para a identificação de um carro em específico e, dessa forma, o portão só iria abrir com a aproximação de um carro autorizado. Neste caso, o portão abre com qualquer objeto que se aproxime no raio de 3 metros.

            Equação 2 -Cálculo da distância entre um objeto e o sensor de ultrassom
                
                distance =((duration/2))/20.7

                duration: tempo entre a emissão e a receção da frequência

        TECLADO COM CÓDIGO DE SEGURANÇA
            Objetivo: Abrir porta de entrada através de código introduzido no keypad
            Componentes utilizados: 
                KEYPAD_EXT_Keypad
                SERVO_INT_Frontdoor
                PIEZO_INT_Alarm
            Implementação: Com a introdução do código PIN (4 dígitos), podemos introduzir o símbolo “ # ” ou esperar para o código ser verificado. Caso seja introduzido um código menor do que 4 dígitos, o PIN não será verificado. O símbolo “ * ” permite limpar o código já introduzido e recomeçar a inserção. Caso o código esteja correto, é enviado o código 400 do Arduino Exterior para o Arduino Interior que fará abrir a porta de entrada (SERVO_INT_Frontdoor). Caso seja inserido um código errado por três vezes consecutivas, será enviado o código 401 do Arduino Exterior para o Arduino Interior que fará o alarme (PIEZO_INT_Alarm) tocar até o código ser inserido corretamente.
            Nota: Para a leitura linha/coluna do keypad ser feita corretamente, nenhuma das linhas poderá estar ligada à entrada 13, visto que, devido a uma função embutida no Arduino, a entrada 13 está ligado ao LED do equipamento e, dessa forma, transmite corrente elétrica que se traduz em erro na leitura de tensão elétrica do keypad.

        BOTÃO PARA ABRIR/FECHAR PORTA DE ENTRADA
            Objetivo: Abrir/fechar a porta de entrada ao pressionar o botão 
            Componentes utilizados: 
                S_INT_DoorButton
                SERVO_INT_Frontdoor
            Implementação: De forma a poder abrir e fechar a porta de entrada (SERVO_INT_Frontdoor) do lado de dentro da casa, foi adicionado um botão (S_INT_DoorButton) que permite abrir a porta quando está fechada e fechar quando está aberta.
            
        CONTROLO REMOTO INFRAVERMELHOS

            INTRODUÇÃO DE CÓDIGO DE SEGURANÇA
                Objetivo: Abrir porta de entrada através da inserção de código
                Componentes utilizados: 
                    U_EXT_InfraredSensor
                    Controlo remoto
                    SERVO_INT_Frontdoor
                    PIEZO_INT_Alarm
                Implementação: Como o sensor de infravermelhos está constantemente a ler a informação enviada pelo controlo remoto, optou-se por, ao ler o sinal da tecla Power (Mapeamento_Controlo_Remoto), entrar num ciclo que regista as teclas pressionadas até ao máximo de 4 teclas ou num intervalo temporal de 5 segundos. Este conjunto de teclas pressionadas será tratado como o código PIN do keypad (ligar alarme ao fim de 3 tentativas sem sucesso ou abrir a porta de entrada após inserção de código válido).

            VISUALIZAÇÃO DAS TEMPERATURAS INTERIOR E EXTERIOR
                Objetivo: Transmitir temperaturas interior e exterior, no LCD
                Componentes utilizados: 
                    U_EXT_InfraredSensor
                    Controlo remoto
                    U_EXT_TemperatureSensor
                    U_INT_TemperatureSensor
                    U_INT_LCD
                Implementação: Ao carregar no botão 0 do controlo remoto, é registada a temperatura do ambiente exterior pelo sensor de temperatura (U_EXT_TemperatureSensor) ligado ao Arduino Exterior, é enviado o código 12 para o Arduino Interior juntamente com a temperatura capturada. No Arduino Interior, o comando é separado da informação da temperatura e é também capturada, através do sensor de temperatura (U_INT_TemperatureSensor) ligado ao Arduino Interior, a temperatura do ambiente interior. Os valores são transmitidos no LCD (U_INT_LCD) (Figura_III_LCD).
                Nota: Os valores capturados pelos sensores de temperatura, não são as temperaturas em graus Celsius e, portanto, têm de ser traduzidas como exemplificado na Equação 1.    

            ALTERAÇÃO DA FREQUÊNCIA (HZ) DO ALARME
                Objetivo: Alterar o somo do alarme para ser mais agudo ou mais grave
                Componentes utilizados: 
                    U_EXT_InfraredSensor
                    Controlo remoto
                    PIEZO_INT_Alarm
                Implementação: Através das teclas VOL+ e VOL- (Mapeamento_Controlo_Remoto), pode-se aumentar ou diminuir a frequência (em hertz), para que o som do alarme seja mais agudo ou mais grave. 
                Nota: No código (ArduinoInterior.txt), é necessário verificar os valores limiares da frequência do alarme, visto que a variação é feita entre 31 e 60500 (exclusivo).

