# Sampling_and_correlation_in_a_comunication_system
Este Projeto implementa a operação de correlação para melhorar a SNR em um sistema de comunicações ideal

#**Amostragem e Sincronização por Correlação**

### **Matemática da Transmissão**
**Codificação**
A codificação é o ato de transformar os caracteres em string que irão ser transmitidos em bits, por exemplo

$$
"A"  -> 0100 0001
$$

$$
"B"  -> 0100 0010
$$

essa conversão pode ser encontrada diretamente na tabela ASCII, e existem funções que ja fazem essa conversão.


Então seja uma sequência a ser transmitida, como por exemplo abaixo

$$
Str = "Olá \ mundo"
$$

Quando codificada, gera-se um vetor com os bits dos símbolos, que depois irá ser convertido em números únicos

$$
s = letrapam(str)
$$

O tipo de codificação será uma 4-PAM
dada pela relação

00 -> -3

01 -> -1

10 ->  1

11 -> 3



**Filtro de pulso**
Depois da codificação, os símbolos dos caracteres precisam ser armazenados em um vetor, para serem transformados em um sinal analógico.

Esse vetor em que os simbolos são adicionados, pode ser representado por um trem de impulsos, dado por

$$
s[n] = \sum_{k = 0}^{M} s_k \cdot δ[n - kM]
$$


e esse sinal de simbolos é convoluído com uma forma de pulso, $p[n]$, e o sinal resultante que será transmitido, fica

$$
x[n] = s[n] \ast p[n] = \sum_{k = 0}^{M} s_k \cdot δ[n - kM] \ast p[n]
$$

então

$$
x[n] =  \sum_{k = 0}^{M} s_k \cdot (p[n] \ast \delta[n-kM]) = \sum_{k = 0}^{M} s_k p[n - kM]
$$

e o sinal $x[n]$ é passado por um conversor D/A para a modulação e posteriormente transmissão.


##**Canal**
Depois de sair do transmissor, o sinal chega ao receptor apresentando algumas distorções de canal

**Ruído aditivo**
O ruído aditivo é a modelagem mais simplificada de um efeito de canal, onda o sinal é somado a um sinal de ruído aleatório gerado, Matematicamente temos

$$
x_{recebido}(t) = x(t) + \eta (t)
$$




###**Sincronização por correlação**
A operação de correlação é feita de modo a aumentar a relação sinal ruído do sinal que é recebido, que normalmente está contaminado com ruído aditivo.

A correlação entre dois sinais é dada por

$$
\phi_{xy} = \sum_{k = -∞ }^{∞} x[k] y[n + k]
$$


que também pode ser vista como uam convolução entre o sinal x e o sinal y refletido, uma vez que

$$
\sum_{k = -∞ }^{∞} x[k] y[n + k] = \sum_{k = -∞ }^{∞} x[k] y[-(-n - k)]
$$

e se temos

$$
y_r[n] = y[-n]
$$

então

$$
\phi_{xy} = \sum_{k = -∞ }^{∞} x[k] y_r[n - k] = x[n] \ast y[-n]
$$

muitas vezes a sincronização é implementada assim, e essa implementação também pode ser chamada de filtro casado(Match Filter).

No contexto dos sistemas de comunicações, a operação de correlação é utilizada para aumentar a relação sinal ruído do sinal recebido, para evitar erros na mensagem transmitida.

Seja o sinal recebido com ruído aditivo de canal dado por

$$
x_r[n] = x[n] + \eta[n]
$$

é então feita a correlação do sinal recebido com o filtro de pulso usado para transformar a mensagem em um sinal banda base, temos então

$$
y[n] = correlacao(x_r[n],p[n]) = \sum_{m = 0}^{N-1} x_r[n] p[n + m]
$$

porém temos ainda que

$$
x_r[n] = x[n] + \eta[n]
$$

e

$$
x[n] = s[n] \ast p[n]
$$

então temos

$$
y[n] = \sum_{m = 0}^{N-1} (s[n] \ast p[n] + \eta[n] ) p[n + m]
$$

distribuindo

$$
y[n] = \sum_{m = 0}^{N-1} (s[n] \ast p[n] ) p[n + m] +  \sum_{m = 0}^{N-1}  \eta[n] p[n + m]
$$

e normalmente o ruído é descorrelacionado com o sinal, assim a parcela de ruído se torna muito próxima de zero, então


$$
y[n] = \sum_{m = 0}^{N-1} (s[n] \ast p[n] ) p[n + m] 
$$

porém é possível perceber que precisamos normalizar ainda o sinal $y[n]$ de acordo com a potência do pulso para que as amplitudes originais se revelem, então

$$
y_{normalizado}[n] = \frac{y[n]}{ E\{\ p[n]^2 \}\ }
$$

porém se a correlação e a convolução são truncadas para o mesmo tamanho do sinal $x[n]$, o sinal $y[n]$ tem o mesmo tamanho que ele, então basta fazermos uma sobreamostragem do sinal(diminuir sua frequência de amostragem), para que consigamos pegar os picos da correlação que correspondem a amplitude dos simbolos.
então o vetor de simbolos recebido finalmente é dado por

$$
s_r[n] = y_{normalizado}[n/M]
$$

para todo $n = kM$.
