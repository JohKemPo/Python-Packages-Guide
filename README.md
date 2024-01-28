
<div align="center">
  <img src="[README] images/setuptools.png"width=" 150px">
  <img src="[README] images/python.png"width=" 150px">
  <h1>Guia prático de Testes unitáriso com Pytest</h1>
</div>
<p align = "justify"> &emsp; Este repositório fornece um guia sobre testes unitários utilizando o framework Pytest. A estrutura pytest facilita a gravação de testes pequenos e legíveis e pode ser dimensionada para suportar testes funcionais complexos para aplicativos e bibliotecas.</p>


<h2 align="center">S U M A R I O</h2>
<br>

1. [Instalação e configuração do Pytest](#capitulo1)
    1. [Instalação](#subcapitulo1_1)
1. [Estrutura básica de um teste usando Pytest](#capitulo2)
    1. [Estrutura de diretórios](#subcapitulo2_1)
    1. [Descoberta de testes em Python](#subcapitulo2_2)
    1. [Asserts e verificações de testes](#subcapitulo2_3)
1. [Fixtures e parametros dinâmicos](#capitulo4)
    1. [O que são fixtures?](#subcapitulo4_1)
    1. [Parametros dinâmicos](#subcapitulo4_2)
1. [Relatórios de testes](#capitulo5)
    1. [Gerando relatórios de cobertura](#subcapitulo5_1)
1. [Mocks e MagicMocks[ EM PRODUÇÃO ]](#capitulo6)
    1. [Subcapitulo](#subcapitulo6_1)
1. [Apêndice](#apendice)
    1. [Ambiente virtual](#env):
        - [Miniconda](#miniconda)
        - [Venv](#venv)

<br><hr>

<!-- CAPITULO 1 -->

<h2 id="capitulo1">Instalação do Pytest:</h2>
<h3 id="subcapitulo1_1">Instalação</h3>
<p align = "justify"> &emsp; Para começar, certifique-se de ter o Python e o gerenciador de pacotes pip instalados em seu sistema. Em seguida, execute o seguinte comando no terminal para instalar o Pytest:
</p>


```bash
# requeriments.txt
pytest==x.x.x    
pytest-cov==x.x.x   
pytest-mock==x.x.x  
pytest-watch==x.x.x     
pytest-xdist==x.x.x     
```

- Instalação das bibliotecas:

```bash
pip install -r requeriments.txt
```
Configurações de ambiente, acesse a seção [Ambiente virtual](#env).



<!-- CAPITULO 2 -->

<h2 id="capitulo2">Estrutura básica de um teste usando Pytest:</h2>

Documentação disponível em [documentação pytest](https://docs.pytest.org/en/7.1.x/getting-started.html#create-your-first-test)


<h3 id="subcapitulo2_1">Estrutura de diretórios</h3>
<p align = "justify"> &emsp;Organize seu projeto em uma estrutura de diretórios. Por exemplo:</p>

```bash
Projeto/
├── app/
│   ├── utils/
│   │   ├── __init__.py
│   │   └── util.py
│   ├── __init__.py
│   └── codigo.py
├── tests/
│   ├── utils/
│   │   └── test_util.py
│   └── test_codigo.py
└── requirements.txt
```

<h3 id="subcapitulo2_2">Descoberta de testes em Python</h3>

Pytest implementa a seguinte descoberta de teste padrão, os nomes dos testes devem seguir o modelo `test_*.py` or `*_test.py`, [Mais informações na documentação](https://docs.pytest.org/en/7.4.x/explanation/goodpractices.html#test-discovery).

<h3 id="subcapitulo2_3">Asserts e verificações de testes.:</h3>

### **Assertions:**

Use as afirmações `assert` para verificar se o resultado esperado é igual ao resultado real. Se a afirmação for falsa, o Pytest relatará um erro.

**Exemplo:**

- Código:
```python
# meu_codigo.py
def multiplicar(a, b):
    return a * b
```
- Teste:
```python
# test/test_meu_codigo.py
import meu_codigo

def test_multiplicar():
    resultado = meu_codigo.multiplicar(3, 4)
    assert resultado == 12, f"Esperado: 12, Obtido: {resultado}"
```

Neste exemplo, o teste verifica se a função multiplicar produz o resultado esperado de 12 ao multiplicar 3 por 4. Se o resultado for diferente de 12, o assert falhará, e o Pytest relatará um erro, **mostrando a mensagem opcional fornecida.**

**Evite Side Effects:**

Evite realizar operações com efeitos colaterais dentro de uma instrução assert. O objetivo é validar o resultado, não modificar o estado.

### **Exemplo prático:**
Caso queria executar os testes eles estão disponíveis em `app_proj/`.

- Código:

```python
# Caminho: app_proj\codes\codigo.py

from app_proj.utils.util import format_message, calculate_sum


class MyClass1:
    def __init__(self, name):
        self.name = name

    def greet(self, use_uppercase: bool, *args, **kwargs):
        message = "Hello, {}!"
        formatted_message = format_message(use_uppercase, message, self.name)
        print(formatted_message.format(*args, **kwargs))

    def calculate_square(self, num: int | float):
        result = num ** 2
        print(f"The square of {num} is {result}")

class MyClass2:
    def __init__(self, value: int | float):
        self.value = value

    def display_info(self, use_uppercase: bool, *args, **kwargs):
        message = "The value is {}."
        formatted_message = format_message(use_uppercase, message, self.value)
        print(formatted_message.format(*args, **kwargs))

    def calculate_cube(self, num: int | float):
        result = num ** 3
        print(f"The cube of {num} is {result}")
```

- Teste:


```python
# Caminho: tests\test_codigo.py

from app_proj.codes.codigo import MyClass1, MyClass2
import pytest

'''
    capsys é uma fixture do pytest que captura a saída do stdout e stderr.
    Fixtures serão mlehor explicadas no próximo capitulo.
'''

# Tests for main.py
def test_myclass1_greet(capsys):
    obj = MyClass1("John")
    obj.greet(True, "Have a nice day!")
    captured = capsys.readouterr()
    assert captured.out.strip() == "HELLO, JOHN!"

def test_myclass1_calculate_square(capsys):
    obj = MyClass1("John")
    obj.calculate_square(4)
    captured = capsys.readouterr()
    assert captured.out.strip() == "The square of 4 is 16"

def test_myclass2_display_info(capsys):
    obj = MyClass2(10)
    obj.display_info(False, "Additional info")
    captured = capsys.readouterr()
    assert captured.out.strip() == "The value is 10."

def test_myclass2_calculate_cube(capsys):
    obj = MyClass2(10)
    obj.calculate_cube(3)
    captured = capsys.readouterr()
    assert captured.out.strip() == "The cube of 3 is 27"
```

- Código:

```python
# Caminho: app_proj\utils\util.py
def format_message(use_uppercase: bool, message: str, *args, **kwargs):
    formatted_message = message.format(*args, **kwargs)
    return formatted_message.upper() if use_uppercase else formatted_message

def calculate_sum(numbers: list):
    return sum(numbers)
```

- Teste:

```python
# Caminho: tests\test_codigo.py
from app_proj.utils.util import format_message, calculate_sum

# Tests for utils.py
def test_format_message():
    assert format_message(True, "Hello, {}!", "John") == "HELLO, JOHN!"
    assert format_message(False, "Hi, {}!", "Alice") == "Hi, Alice!"

def test_calculate_sum():
    numbers = [1, 2, 3, 4, 5]
    assert calculate_sum(numbers) == 15
    assert calculate_sum([]) == 0
```
### **Executanto um teste:**

```bash
> python -m pytest tests\test_codigo.py
```

**Esse é o resultado da execução dos testes**

```
=========================== test session starts ============================
platform win32 -- Python 3.12.0, pytest-7.4.4, pluggy-1.3.0
rootdir: C:\Users\japag\OneDrive\Documentos\GIT\Pytest-for-Unit-tests-Guide
collected 4 items

tests\test_codigo.py ....                                             [100%]

============================ 4 passed in 0.01s =============================
```

**Número de Itens Coletados e Status da Execução:**

```bash
collected 4 items
```

- Mostra que o Pytest encontrou e coletou 4 itens de teste.

**Execução dos Testes:**

```bash
tests\test_codigo.py ....  
```

- Mostra o progresso da execução dos testes. Cada ponto **`.`** representa um teste que foi executado com sucesso. O `[100%]`indica que todos os testes foram executados.


<!-- CAPITULO 3 -->

<h2 id="capitulo4">Fixtures e parametros dinâmicos:</h2>
<h3 id="subcapitulo4_1">O que são fixtures?</h3>
<p align = "justify"> &emsp;Em Pytest, as fixtures são um recurso usados para configurar o estado inicial para testes e fornecer dados pré-definidos ou recursos compartilhados, como inputs, instancias de classes, e etc. Elas permitem que você configure ambientes específicos para testes, definindo recursos que podem ser usados por vários testes ou escopos específicos.</p>


### **Exemplo básico:**

```python
# meu_codigo.py
def calcular(a, b):
    return a + b
```

- Teste usando fixtures:

```python
# test/test_meu_codigo.py
import pytest
from meu_codigo import calcular

# Fixture que fornece dois números para testar
@pytest.fixture
def numeros_para_teste():
    return 3, 4

# Teste utilizando a fixture
def test_calcular_soma(numeros_para_teste):
    a, b = numeros_para_teste
    resultado = calcular(a, b)
    assert resultado == 7
```

**Neste exemplo:**

`numeros_para_teste` é uma fixture que fornece os números 3 e 4.
O teste test_calcular_soma aceita numeros_para_teste como um argumento. A fixture é automaticamente injetada pelo Pytest.

**Escopos de Fixtures:**

As fixtures podem ter diferentes escopos:

- `Função (padrão)`: A fixture é executada uma vez para cada função de teste.
- `Módulo`: A fixture é executada uma vez para cada módulo de teste.
- `Classe`: A fixture é executada uma vez para cada classe de teste.
- `Sessão`: A fixture é executada uma vez para toda a sessão de teste.

<h3 id="subcapitulo4_2">Parâmetros Dinâmicos</h3>

Você pode parametrizar fixtures para fornecer dados diferentes a cada teste.

```python
@pytest.fixture(params=[(3, 4), (5, 6)])
def numeros_para_teste(request):
    return request.param
```

Parametrização de fixtures é uma funcionalidade do Pytest que permite fornecer diferentes conjuntos de dados ou configurações para testes específicos. Isso é útil quando você tem um conjunto de testes que segue um padrão semelhante, mas precisa ser executado com diferentes conjuntos de entradas.


### **Exemplo básico:**

```python
import pytest
from meu_codigo import somar

# Fixture parametrizada
@pytest.fixture(params=[(2, 3), (5, 7), (10, 15)])
def numeros_para_teste(request):
    return request.param

# Teste usando a fixture parametrizada
def test_somar(numeros_para_teste):
    a, b = numeros_para_teste
    resultado = somar(a, b)
    assert resultado == a + b
```

**Neste exemplo:**

- A fixture numeros_para_teste é parametrizada com uma lista de tuplas contendo diferentes pares de números.
- A cada execução do teste, a fixture é chamada com um conjunto diferente de números.

### **Parametrização de Escopo Mais Amplo:**

Você pode combinar a parametrização de fixtures com escopos diferentes. Por exemplo, se você quiser que a parametrização seja feita apenas uma vez por módulo, pode usar o escopo de módulo:

```python
import pytest
from meu_codigo import somar

# Fixture parametrizada com escopo de módulo
@pytest.fixture(params=[(2, 3), (5, 7), (10, 15)], scope="module")
def numeros_para_teste(request):
    return request.param

# Teste usando a fixture parametrizada
def test_somar(numeros_para_teste):
    a, b = numeros_para_teste
    resultado = somar(a, b)
    assert resultado == a + b
```

### **Casos de Uso Comuns:**

**Testar com Diferentes Entradas:**

- Como ilustrado acima, você pode testar uma função com diferentes conjuntos de entradas.

**Configurações com Dados Variados:**

- Parametrize fixtures para configurar ambientes de teste com diferentes conjuntos de dados ou configurações.

**Avaliação de Comportamento com Dados Diversos:**

- Testar se uma função se comporta corretamente com diferentes tipos de entradas.

A marcação `@pytest.mark.parametrize` é uma maneira alternativa de parametrizar testes em Pytest, que pode ser usada diretamente em funções de teste, sem a necessidade de criar uma fixture separada para isso.

A marcação `@pytest.mark.parametrize` permite que você defina diferentes conjuntos de parâmetros para a mesma função de teste. Ela aceita um nome de parâmetro, uma lista de valores e gera automaticamente instâncias da função de teste para cada combinação de parâmetros.

**Exemplo básico:**

```python
import pytest
from meu_codigo import somar

# Teste parametrizado usando a marcação pytest.mark.parametrize
@pytest.mark.parametrize(
        "a, b, esperado", [(2, 3, 5), # teste 1
                           (5, 7, 12), # teste 2
                           (10, 15, 25)]) # teste 3
def test_somar(a, b, esperado):
    resultado = somar(a, b)
    assert resultado == esperado
```

- A marcação @pytest.mark.parametrize é aplicada à função de teste test_somar.

- Os parâmetros "a", "b" e "esperado" são fornecidos como strings, seguidos por uma lista de tuplas contendo valores específicos para cada execução do teste.

- O Pytest gerará automaticamente instâncias separadas da função test_somar para cada conjunto de parâmetros fornecido, resultando em três testes distintos.



<!-- CAPITULO 4 -->

<h2 id="capitulo5">Execução de relatórios dos testes:</h2>
<h3 id="subcapitulo5_1">Gerando relatórios de cobertura:</h3>

### **Gerando relatório de cobertura de um teste específico no terminal:**

```bash
> python -m pytest tests\test_codigo.py --cov
```
```bash
============================== test session starts ===============================
platform win32 -- Python 3.11.4, pytest-7.2.1, pluggy-1.0.0
rootdir: ~\Pytest-for-Unit-tests-Guide
plugins: cov-4.0.0, mock-3.10.0, xdist-3.2.1
collected 4 items

tests\test_codigo.py ....                                                   [100%]

---------- coverage: platform win32, python 3.11.4-final-0 -----------
Name                         Stmts   Miss  Cover
------------------------------------------------
app_proj\__init__.py             0      0   100%
app_proj\codes\__init__.py       0      0   100%
app_proj\codes\codigo.py        33      9   100%
app_proj\utils\__init__.py       0      0   100%
app_proj\utils\util.py           5      1   100%
tests\test_codigo.py            22      0   100%
------------------------------------------------
TOTAL                           60     10    83%


=============================== 4 passed in 0.06s ================================ 
```

- `tests\test_codigo.py`: Especifica o caminho para o arquivo de teste que você deseja executar. Neste caso, os testes serão executados no arquivo test_codigo.py dentro do diretório tests.

- `--cov`: Indica ao Pytest para coletar informações de cobertura durante a execução dos testes.

- Este comando executará os testes no arquivo `tests\test_codigo.py` e calculará a cobertura **dos código associado aos testes.**

### **Gerando relatório de cobertura de um repositório específico no terminal:**

```bash
> python -m pytest tests\ --cov
```
```bash

=============================== test session starts ================================
platform win32 -- Python 3.11.4, pytest-7.2.1, pluggy-1.0.0
rootdir: ~\Pytest-for-Unit-tests-Guide
plugins: cov-4.0.0, mock-3.10.0, xdist-3.2.1
collected 6 items

tests\test_codigo.py ....                                                     [ 66%]
tests\utils\test_util.py ..                                                   [100%] 

---------- coverage: platform win32, python 3.11.4-final-0 -----------
Name                         Stmts   Miss  Cover
------------------------------------------------
app_proj\__init__.py             0      0   100%
app_proj\codes\__init__.py       0      0   100%
app_proj\codes\codigo.py        23      0   100%
app_proj\utils\__init__.py       0      0   100%
app_proj\utils\util.py           5      0   100%
tests\test_codigo.py            22      0   100%
tests\utils\test_util.py         8      0   100%
------------------------------------------------
TOTAL                           58      0   100%


================================ 6 passed in 0.07s =================================
```
- Usado para executar testes, juntamente com a geração de um relatório de cobertura do diretorio usando o pacote coverage.

- `tests\`: Especifica o diretório onde os testes devem ser procurados. Neste caso, os testes estarão no diretório tests.

 - `--cov`: Indica ao Pytest para coletar informações de cobertura durante a execução dos testes.

Se você executar esse comando, o Pytest executará todos os testes no diretório tests e gerará um relatório de cobertura, indicando quais partes do código foram cobertas pelos testes. O resultado será exibido no console e, dependendo da configuração, também pode gerar um relatório mais detalhado em HTML.

### **Gerando relatório de cobertura de um repositório específico e salvando em html:**

```bash
> python -m pytest --cov-report html:coverage/ --cov=app_proj/
```
```bash
=============================== test session starts ================================ 
platform win32 -- Python 3.11.4, pytest-7.2.1, pluggy-1.0.0
rootdir: ~\Pytest-for-Unit-tests-Guide
plugins: cov-4.0.0, mock-3.10.0, xdist-3.2.1
collected 6 items

tests\test_codigo.py ....                                                     [ 66%] 
tests\utils\test_util.py ..                                                   [100%] 

---------- coverage: platform win32, python 3.11.4-final-0 -----------
Coverage HTML written to dir coverage/


================================ 6 passed in 0.12s =================================
```

 - `--cov-report html:coverage/`: Esta parte do comando especifica que o relatório de cobertura deve ser gerado no formato HTML e salvo no diretório coverage/. Isso criará uma visualização mais detalhada da cobertura de código em formato HTML.

- `--cov=app_proj/`: Indica ao Pytest para coletar informações de cobertura para o diretório app_proj/. Isso significa que a cobertura será calculada para o código dentro desse diretório.

### **Relatórios Verbosos no terminal:**
```bash
> python -m pytest -v            
```
```bash
===================================== test session starts =====================================
platform win32 -- Python 3.11.4, pytest-7.2.1, pluggy-1.0.0 -- 
cachedir: .pytest_cache
rootdir: ~\Pytest-for-Unit-tests-Guide
plugins: cov-4.0.0, mock-3.10.0, xdist-3.2.1
collected 6 items

tests/test_codigo.py::test_myclass1_greet PASSED                                         [ 16%] 
tests/test_codigo.py::test_myclass1_calculate_square PASSED                              [ 33%]
tests/test_codigo.py::test_myclass2_display_info PASSED                                  [ 50%] 
tests/test_codigo.py::test_myclass2_calculate_cube PASSED                                [ 66%] 
tests/utils/test_util.py::test_format_message PASSED                                     [ 83%] 
tests/utils/test_util.py::test_calculate_sum PASSED                                      [100%] 

====================================== 6 passed in 0.04s ======================================
```
 - Exibe informações detalhadas sobre a execução dos testes, incluindo o nome de cada teste e seu resultado.

<!-- APENDICE -->

<h2 id="apendice">Apêndice </h2>

<h3 id="env">Criação  de Ambiente Virtual em Python e instalação das dependências</h3>

**`Crie somente um ambiente virtual, após isso instale as dependências descritas na fase de instalação de dependências.`**

<br>
<h3 id="miniconda">(Opçãp 1) Criação - miniconda:</h3>

1. Baixar o instalador miniconda:
```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```
2. Executando o instalador:
```
bash Miniconda3-latest-Linux-x86_64.sh
```
3. Iniciar coda:
```
conda init
```
4. Listar env existentes:
```
conda env list
```
5. Criar env com pytoh3.10:
```
conda create -n <name env> python=3.10
```
6. Ativar env:
```
conda activate <name env>
```

7. Deletar .sh
```
rm Miniconda3-latest-Linux-x86_64.sh
```

<br>
<h3 id="venv">(Opçãp 2) Criação - venv:</h3>

Para criar um ambiente virtual em Python, você pode usar a biblioteca padrão chamada `venv`. Siga as etapas abaixo para criar e ativar um ambiente virtual usando o `venv`:

1. Verifique se o Python 3 está instalado:
Abra o terminal e execute o seguinte comando:
```
python3 --version
```
2. Se o Python 3 já estiver instalado, você verá a versão instalada. Caso contrário, siga para o próximo passo.

3. Instale o Python 3:

No terminal, execute os comandos apropriados de acordo com a distribuição Linux que você está usando.

```
sudo apt install python3
```

4. Instale o pip:

```
sudo apt install python3-pip
```

5. Instale o pacote venv:
O pacote venv permite criar ambientes virtuais isolados. No terminal, execute o seguinte comando:

```
sudo apt install python3-venv
```

6. Para criação do ambiente virtual:
Navegue até o diretório onde deseja criar o ambiente virtual.

7. Digite o seguinte comando para criar um novo ambiente virtual:

```
python3 -m venv nome_do_ambiente
```

*Substitua "nome_do_ambiente" pelo nome que você deseja dar ao seu ambiente virtual.*

8. Para ativar o ambiente virtual, execute o comando apropriado de acordo com o seu sistema operacional:

```
source nome_do_ambiente/bin/activate
```

9. Agora, o ambiente virtual está ativado. Você pode instalar pacotes e executar seus projetos dentro dele sem afetar o ambiente global do Python.

Quando você terminar de trabalhar com o ambiente virtual, pode desativá-lo usando o comando:

```
deactivate
```

### **Extra**

Configurar para conda sempre inciarlizar em uma determinada env:

```
conda env list
export CONDA_DEFAULT_ENV="/caminho/para/env"
```

### **Instalação das dependências**

1. Instalação das dependências do projeto no **ambiente virtual**:

```
# instalar bibliotecas especificadas em um único arquivo
pip install -r requirements.txt

# instalar bibliotecas especificadas em multiplos arquivos
pip install -r requirements_1.txt -r requirements_2.txt
```

<h3 id="links">Links importantes:</h3>

- [Get Started](https://docs.pytest.org/en/7.1.x/getting-started.html#create-your-first-test)
- [How to invoke pytest](https://docs.pytest.org/en/7.1.x/how-to/usage.html#usage)
- [Fixtures reference](https://docs.pytest.org/en/7.1.x/reference/fixtures.html#fixtures)
    - [About fixtures](https://docs.pytest.org/en/7.1.x/explanation/fixtures.html#about-fixtures)
    - [How to use fixtures](https://docs.pytest.org/en/7.1.x/how-to/fixtures.html#how-to-fixtures)
- [API Reference](https://docs.pytest.org/en/7.1.x/reference/reference.html?highlight=parametrize#pytest-raises)
- [Parametrize](https://docs.pytest.org/en/7.1.x/reference/reference.html?highlight=parametrize#pytest-mark-parametrize)
- [How to parametrize fixtures and test functions](https://docs.pytest.org/en/7.1.x/how-to/parametrize.html#parametrize)
- [Good Integration Practices](https://docs.pytest.org/en/7.1.x/explanation/goodpractices.html#goodpractices)
- [mock object library](https://docs.python.org/3/library/unittest.mock.html)
- [Github Pytest OFICIAL](https://github.com/pytest-dev/pytest)

<br>
<h1 id="Equipe">Equipe</h1><br>

<div align="center">

|     Desenvolvedor              |           GitHub             |       LinkedIn     |
|--------------------------------|------------------------------|--------------------|
|👤 João Vitor Moraes            |<https://github.com/JohKemPo>   |<https://www.linkedin.com/in/joao-vitor-de-moraes/>|
</div>
