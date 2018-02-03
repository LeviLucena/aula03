# aula03
Eu quero!

Search

Code a Test
Vida longa aos testes!
BOAS PRÁTICAS ENGENHARIA DE SOFTWARE SELENIUM TESTES DE ACEITAÇÃO
Page Object: a chave para tornar seus testes de aceitação mais organizados!
Posted on 25/08/2016
 Imagem principal do post
Introdução
Automatizar testes de software tem se tornado quase que uma regra geral no desenvolvimento de aplicações. Os testes de aceitação não fogem a essa regra – no post anterior mostrei como você pode automatizar os testes de aceitação de uma aplicação web utilizando o Selenium WebDriver. Cada vez mais e mais estamos escrevendo e nos deparando com testes automatizados de interface – o que é muito bom. Mas se há uma certeza na Engenharia de Software é que código escrito é código que vai mudar. Ou seja, esse código vai precisar, em algum momento, ser mantido: sofrerá alterações, correções etc. Precisamos, portanto, de uma forma de organizar melhor esse código. O padrão Page Object é uma forma de fazer isso.

Este post vai mostrar como utilizar o padrão Page Object para organizar melhor o código de teste de aceitação de sua aplicação web.

Ainda não sabe como automatizar os testes de aceitação de sua aplicação web? Clique aqui e veja como!

A mudança é inevitável
Como foi mostrado no post anterior, o código utilizando a API do Selenium WebDriver manipula diretamente o HTML da página: encontra elementos, aplica ações etc. Um código que manipula diretamente o HTML é um código que está bastante sujeito a modificação. Essas modificações podem decorrer de:

Modificações de requisitos. Nesse caso, uma modificação na regra de negócio pode resultar em uma mudança em uma determinada página HTML: adicionando ou removendo novos elementos, alterando mensagens de aviso ao usuário etc. Nesses casos, necessariamente, o código de teste que manipula a página modificada também terá que mudar.
Modificações de usabilidade/design de interface. Imagine que em uma determinada página, por questões de usabilidade, decide-se trocar a forma como uma caixa de seleção foi implementada. Antes ela era implementada por meio de um select HTML; na modificação, a caixa de seleção passa a ser implementada por meio de um div HTML. Essa mudança afeta diretamente um código que automatize uma escolha nesta caixa de seleção; logo, modificação inevitável nesse código.
Evolução/troca de tecnologia. A aplicação utilizava como tecnologia de implementação na camada de visão o JSF. Agora vai passar a utilizar Bootstrap + AngularJS. Com isso, o código das páginas vai mudar consideravelmente. O código que automatiza os testes tem que acompanhar essas mudanças.
Portanto o código que realiza a automação de testes de interface é muito sensível à mudança. Quem trabalha com aplicações Web sabe que mudanças em telas é uma realidade permanente. Precisamos de uma solução para o seu problema.

Page Object: trate seu código de teste como você trata o seu código de produção
Quando estamos escrevendo código para a nossa aplicação – o código que vai para a produção – sempre buscamos usar as melhores práticas de Engenharia de Software. No caso da orientação a objeto, procuramos utilizar práticas como reuso, componentização, design patterns, separação de responsabilidades etc. Um dos objetivos de utilizar essas boas práticas é tornar tanto a evolução quanto a manutenção do software mais fácil.

Assim como no nosso código de produção, também temos de cuidar bem do nosso código de testes. Afinal ele também vai precisar ser evoluído e mantido – e queremos que isso seja o menos trabalhoso possível. Nos casos de testes de aceitação automatizados, que testam interfaces do usuário, podemos utilizar o Page Object para atingir esse objetivo.

O Page Object é um padrão que funciona como interface de acesso a elementos da camada de visão – para aplicações web ele representa uma página HTML. Ele é aplicado para abstrair as páginas de uma aplicação com o objetivo de reduzir o acoplamento entre os casos de teste e a aplicação a ser testada.

Vantagens de se utilizar o Page Object
O seu uso traz as seguintes vantagens:

Encapsulamento: o acesso a elementos da página fica encapsulado dentro do Page Object. O que ele nos fornece são métodos para pedirmos que ele realize ações sobre a página a ser testada.
Abstração: para o código de teste não deve importar se no momento de fazer o login em um formulário do tipo usuário/senha, o preenchimento dos campos é feito por meio de um findElement(By.name("")) ou findElement(By.id("")). O código de teste simplesmente deveria informar o usuário/senha e esperar que a autenticação seja feita com sucesso ou não. Detalhes da implementação – que podem vir a mudar – são abstraídos pelo Page Object.
Separação de Responsabilidades: o código de testes não deve ser responsável por fazer uso da API do WebDriver. Não é responsabilidade dele conhecê-la. Um código típico de teste segue um padrão comum: preparação dos dados; execução/chamada do código a ser verificado; e verificação do resultado dessa execução (asserções). Com o uso do Page Object a separação torna-se nítida: o código de teste cuida exclusivamente dos testes; o Page Object é quem sabe como acessar os elementos e realizar ações em determinada página.
Vamos mostrar um exemplo prático de código para tornar mais claro o que é o Page Object e qual a sua utilidade.

Primeiro: código de teste sem o uso do Page Object
Para o nosso exemplo, vamos utilizar uma aplicação web que escrevi e que se encontra em meu GitHub. É uma aplicação conceito de uma ferramenta de gerenciamento de casos de teste. Ela foi implementada utilizando AngularJS e Spring MVC.

Nota: essa aplicação utiliza como base de dados um banco HSQLDB que reside na memória. Para que os testes funcionem é necessário:

colocar a aplicação em um servidor web (por exemplo, o Apache Tomcat)
executar o script ant startDB.xml (que se encontra na raiz do projeto) para iniciar o banco de dados.
Vamos testar o cenário de autenticação com sucesso. A autenticação é feita por meio de um formulário HTML do tipo usuário/senha; sendo feita com sucesso, o usuário é redirecionado para a página inicial da aplicação – a página de listagem de projetos (veja as figuras a seguir).

Tela de login na aplicação
Tela de login na aplicação
Página inicial da aplicação - listagem de projetos
Página inicial da aplicação – listagem de projetos
O código abaixo mostra o caso de teste de autenticação com sucesso.

public class LoginSistemaSemPageObjectTest {
 
    private WebDriver driver;
 
    @Before
    public void before() {
      driver = new ChromeDriver();
    }
 
    @Test
    public void loginComSucesso() {
      // Acessa a página inicial que nos redireciona para a página de login
      driver.get("http://localhost:8080/simpletests");
 
      // Informa o usuário/senha e clica no botão de login
      driver.findElement(By.name("username")).sendKeys("joao@mpf.mp.br");
      driver.findElement(By.name("password")).sendKeys("123456");
      driver.findElement(By.cssSelector("input[type='submit'")).click();
 
      // Se a página inicial foi acessada: a barra de navegação existe e ela
      // é a página de projetos
      assertNotNull(driver.findElement(By.className("navbar")));
      assertThat(driver.findElement(By.tagName("h2")).getText(), containsString("Projetos"));
    }
 
    @After
    public void after() {
      driver.quit();
    }
 
}

The Maven with Travis plus
