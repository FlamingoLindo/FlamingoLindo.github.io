---
layout: post
title:  "Introduction to Selenium/Appium/RobotFramework"
date:   2024-11-29 19:17:58 -0300
categories: python selenium appium automation
---

# What is this repository about?

This repository aims at trying to teach the basics of Selenium, Appium and RobotFramework for those who want to get into automation and find it hard to learn from old tutorials
with wrong information or decrapeted stuff.

I actually made this after I had to teach someone from work and I struggle to remember some installation processes and some of the commands.

You can find the repository [here](https://github.com/FlamingoLindo/Introducao-Selenium-Appium-RobotFramework).

---

# Here are some snippets of what you can find in there

Unfortunately I only had time to make the tutorial in portuguese, I genuinely want to make this tutorial aviable in english...

## Selenium

```python
"""
CLICANDO E DIGITANDO EM ELEMENTOS DA PÁGINA
"""

from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import time
driver = webdriver.Chrome()
wait = WebDriverWait(driver, 5)

driver.get('https://www.twitch.tv/flamingo_lindo')

register_btn = wait.until(EC.element_to_be_clickable((By.XPATH, '//*[@id="root"]/div/div[1]/nav/div/div[3]/div[3]/div/div[1]/div[2]/button/div/div'))).click()

modal = wait.until(EC.visibility_of_element_located((By.ID, 'modal-root-header')))

"""
Aqui nós estamos procurando a caixa de texto com o ID igual á "signup-username" e então utilizamos a função
"clear()" para limpar a caixa de texto caso haja alguma informção já lá dentro.

E por utlimo é utilizado a função "send_keys()" que dentro dela pode ser passado um texto ou números, mas é recomando 
sempre passar os valores em forma de texto, String.
"""
name_input = driver.find_element(By.ID, 'signup-username')
name_input.clear()
name_input.send_keys('usuarioAutomatico312123')

password_input = driver.find_element(By.ID, 'password-input')
password_input.clear()
password_input.send_keys('Aa12345678!')

"""
Nesse elemento é utilizado a funcção "click()" que como o próprio nome diz, é utilizado para clicar no elemento.
"""
day = driver.find_element(By.XPATH, '/html/body/div[3]/div/div/div/div/div/div[1]/div[1]/div/div/div[2]/form/div/div[3]/div/div[2]/div[1]/div/select')
day.click()

select_day = wait.until(EC.element_to_be_clickable((By.XPATH, '/html/body/div[3]/div/div/div/div/div/div[1]/div[1]/div/div/div[2]/form/div/div[3]/div/div[2]/div[1]/div/select/option[16]')))
select_day.click()

month = driver.find_element(By.XPATH, '/html/body/div[3]/div/div/div/div/div/div[1]/div[1]/div/div/div[2]/form/div/div[3]/div/div[2]/div[2]/div/select')
month.click()

month_select = wait.until(EC.element_to_be_clickable((By.XPATH, '/html/body/div[3]/div/div/div/div/div/div[1]/div[1]/div/div/div[2]/form/div/div[3]/div/div[2]/div[2]/div/select/option[3]')))
month_select.click()

year = driver.find_element(By.XPATH, '/html/body/div[3]/div/div/div/div/div/div[1]/div[1]/div/div/div[2]/form/div/div[3]/div/div[2]/div[3]/div/select')
year.send_keys(2003)

email_btn = driver.find_element(By.XPATH, '/html/body/div[3]/div/div/div/div/div/div[1]/div[1]/div/div/div[2]/form/div/div[4]/div[1]/div/div[1]/button/div/div')
email_btn.click()

email_input = wait.until(EC.element_to_be_clickable((By.ID, 'email-input')))
email_input.send_keys('autoemail123@gmail.com')


done_btn = wait.until(EC.element_to_be_clickable((By.XPATH, '/html/body/div[3]/div/div/div/div/div/div[1]/div[1]/div/div/div[2]/form/div/div[6]/div/button/div')))
time.sleep(0.5)
done_btn.click()

alert = wait.until(EC.visibility_of_element_located((By.CLASS_NAME, 'server-message-alert')))
time.sleep(5)

driver.quit()


"""
AVISO!!!

Não é possivel chamar mais de uma funcção em um mesmo elemento ao mesmo tempo, na mesma linha ou em diferentes:
password_input = driver.find_element(By.ID, 'password-input').clear().send_keys('Aa12345678!')

---

Mas é possível chamar uma função só em um elemento:
password_input = driver.find_element(By.ID, 'password-input').clear()

"""
```

## Appium

```python
"""
INTERAGINDO
"""

import unittest
from appium import webdriver
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from appium.options.android import UiAutomator2Options
from appium.webdriver.common.appiumby import AppiumBy

capabilities = dict(
    noReset = True,
    automationName='uiautomator2',
    language='pt',
    printPageSourceOnFindFailure = True,
    appPackage='com.google.android.youtube',
    appActivity='com.google.android.youtube.app.honeycomb.Shell$HomeActivity'
)

appium_server_url = 'http://localhost:4723'

class TestAppium(unittest.TestCase):
    def setUp(self) -> None:
        self.driver = webdriver.Remote(appium_server_url, options=UiAutomator2Options().load_capabilities(capabilities))

    def tearDown(self) -> None:
        if self.driver:
            self.driver.quit()

    def test_nome_do_teste(self) -> None:
        wait = WebDriverWait(self.driver, 5)
        
        search_icon = wait.until(EC.presence_of_element_located((AppiumBy.ACCESSIBILITY_ID, 'Pesquisar')))
        search_icon = wait.until(EC.element_to_be_clickable((AppiumBy.ACCESSIBILITY_ID, 'Pesquisar')))
        search_icon.click()

        search_bar = wait.until(EC.element_to_be_clickable((AppiumBy.ID, 'com.google.android.youtube:id/search_edit_text')))
        search_bar.send_keys('Velvet Revolver - Fall To Pieces')

        result = wait.until(EC.element_to_be_clickable((AppiumBy.ANDROID_UIAUTOMATOR, 'new UiSelector().resourceId("com.google.android.youtube:id/linearLayout").instance(0)')))        
        result.click()
        
        video = wait.until(EC.visibility_of_element_located((AppiumBy.XPATH, '//android.support.v7.widget.RecyclerView[@resource-id="com.google.android.youtube:id/results"]/android.view.ViewGroup[4]')))
        video.click()


if __name__ == '__main__':
    unittest.main()
```

## RobotFramework

# Criando um teste

Dentro da pasta `Tests` crie um arquivo chamado `create_accout.robot`

Este aquivo será utilizado para criar e orgarnizar os testes:

```robot
*** Settings ***
Library    SeleniumLibrary
Library    XML
Resource   ../Keywords/keywords.robot
Resource   ../Resources/variables.robot

*** Test Cases ***
Create account
    Open the url in browser    ${page}    ${browser}
    
    Wait for and click in the log-in button

    Wait for modal to load

    Fill up the form    ${name}    ${password}    ${email}

    Wait and see the results

    [Teardown]    Close Browser
```

* `*** Settings ***` → São declaradas as bibliotecas e recursos necessários para o teste.

* `Library    SeleniumLibrary` →  Biblioteca essencial para automatizar ações em navegadores, como abrir uma página, clicar em botões, preencher formulários, entre outras interações. É importada para permitir que o teste realize ações no navegador.

* `Library    XML` → É uma biblioteca utilizada para manipulação de documentos XML. Embora não seja utilizada diretamente no exemplo fornecido, pode ser útil para manipular ou validar dados em formato XML em outros casos de teste.

* `Resource   ../Keywords/keywords.robot` → O arquivo keywords.robot contém palavras-chave personalizadas (ações específicas que o teste executa). Ele é importado para que essas palavras-chave possam ser usadas neste arquivo de teste.

* `Resource   ../Resources/variables.robot` → O arquivo variables.robot contém as variáveis usadas no teste, como URL, navegador, nome de usuário, etc. Ele é importado para fornecer esses valores no momento da execução do teste.

* `*** Test Cases ***` → Cada caso de teste consiste em uma série de palavras-chave que serão executadas.

* `Create account` → Este é o nome do caso de teste. O nome serve para identificar o teste e deve descrever de maneira clara o que está sendo testado. No caso, estamos criando uma conta, então o nome do caso de teste reflete isso.

* `Open the url in browser    ${page}    ${browser}` → Palavra-chave personalizada que abre o navegador e acessa a URL. Os valores `${page}` (URL) e `${browser}` (navegador) são variáveis que são passadas a partir do arquivo `variables.robot`.

* `Wait for and click in the log-in button` → Espera até que o botão de login esteja visível e então clica nele. Isso é útil quando a página pode levar um tempo para carregar completamente. A palavra-chave vai garantir que o botão de login esteja visível antes de tentar clicar.

* `Wait for modal to load` → Palavra-chave personalizada que aguarda o carregamento de um modal (caixa de diálogo ou janela pop-up) na página. Ela usa um comando Wait Until Element Is Visible para garantir que o modal seja carregado e visível.

* `Fill up the form    ${name}    ${password}    ${email}` → Palavra-chave personalizada que preenche um formulário com os dados do usuário. Os parâmetros `${name}`, `${password}` e `${email}` são passados como variáveis (definidas no arquivo variables.robot) e inseridos nos campos apropriados da página.

* `Wait and see the results` → Palavra-chave personalizada que espera (com o comando Sleep) por 15 segundos. Isso é útil para ver o que acontece após a execução do formulário e o login. Em muitos casos de automação, usar Sleep é uma maneira simples de garantir que os resultados sejam visíveis antes de o teste ser finalizado, mas deve ser usado com cautela, pois pode tornar os testes mais lentos.

* `[Teardown]    Close Browser` → É uma seção que define ações que devem ser realizadas após a execução do caso de teste, independentemente de o teste ter passado ou falhado. Neste caso, a palavra-chave Close Browser é chamada para fechar o navegador após o término do teste, garantindo que os recursos sejam liberados corretamente.

# Executando um teste

Para executar um teste, basta apenas clicar em `run` que aparece em cima do nome do caso de teste.

![exc](https://github.com/FlamingoLindo/Introducao-Selenium-Appium-RobotFramework/blob/main/Images/Robot/7.png?raw=true)

Após a execução do testeé possivel ver que ele passou sem nenhum erro e que alguns arquis foram criados no _root_ do projeto.

![result](https://github.com/FlamingoLindo/Introducao-Selenium-Appium-RobotFramework/blob/main/Images/Robot/8.png?raw=true)

Se clicar no segundo caminho **Log:** nele com `Ctrl + botão esquerdo o mouse`

![log](https://github.com/FlamingoLindo/Introducao-Selenium-Appium-RobotFramework/blob/main/Images/Robot/9.png?raw=true)

O **Log** contém um registro detalhado da execução do teste, permitindo verificar o que aconteceu durante a execução. Ele fornece informações sobre:

* Passos do Teste: Cada palavra-chave que foi executada, com detalhes sobre o que aconteceu em cada uma delas (se o passo foi bem-sucedido ou se ocorreu algum erro).

* Mensagens de Erro: Se algum erro ocorrer durante o teste (por exemplo, se um elemento não for encontrado ou uma interação falhar), o log exibirá uma mensagem detalhada sobre o erro, ajudando a identificar o que deu errado.

* Captura de Tela (se configurado): Caso o teste esteja configurado para tirar capturas de tela quando um erro ocorrer, o log pode incluir essas imagens.

* Tempo de Execução: O tempo que cada parte do teste levou para ser executada, podendo ser útil para análise de desempenho.

A interface do Log normalmente exibe o histórico completo da execução e permite navegar por cada passo do teste, ajudando na depuração e na análise de falhas.

Se clicar no terceiro caminho **Report:** nele com `Ctrl + botão esquerdo o mouse`

![report](https://github.com/FlamingoLindo/Introducao-Selenium-Appium-RobotFramework/blob/main/Images/Robot/10.png?raw=true)

O **Report** fornece um resumo visual e consolidado da execução do teste. Ele geralmente contém:

* Resumo do Teste: Informações gerais sobre a execução, como a quantidade de testes executados, passados, falhados e ignorados.
Status Global: Um status geral da execução (se foi bem-sucedida ou se houve falhas).

* Testes Detalhados: Detalhes sobre cada caso de teste, como o nome do teste, o tempo de execução e o status (passou ou falhou).

* Histórico de Execuções: Em alguns casos, o Report pode incluir uma comparação com execuções anteriores, permitindo avaliar se o número de falhas ou testes bem-sucedidos está mudando ao longo do tempo.

O Report é uma forma mais resumida e amigável de visualizar os resultados do teste, com ênfase na interpretação rápida dos dados, sem tantos detalhes como o Log.

**Diferença entre Log e Report:**

**Log:** É o registro detalhado de tudo o que aconteceu durante a execução, útil para depuração e entendimento profundo do fluxo de execução.

**Report:** É o resumo visual da execução, focado no status geral dos testes, ideal para ter uma visão rápida do desempenho dos testes.

Ambos são ferramentas importantes para verificar o sucesso ou falha de um teste e diagnosticar problemas, mas com diferentes níveis de detalhamento.
