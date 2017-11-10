Seu aplicativo geralmente irá precisa permitir que os usuários interajam com
controles que alteram o estado do seu aplicativo. Por exemplo, você precisa que tenha um modelo que mostre um título de blog e suporte a expansão do post para mostrar o conteúdo.

Se você adicionar um método ```{{action}}``` a qualquer elemento DOM do seu documento, quando um usuário clicar no elemento, o evento que você definiu será enviado ao componente ou ao controlador correspondente.

```html
<h3>
  <button {{action 'toggleBody'}}> {{ title }} </button>
  {{#if isShowingBody }}
    <p>
      {{ body }}
    </p>
  {{/if }}
</h3>
```

No componente ou no controlador, você definir a ```{{action}}``` que o evento irá disparar:

```js
import Ember from 'ember';

export default Ember.Component.extend({
  actions: {
    toggleBody() {
      this.toggleProperty('isShowingBody');
    }
  }
});
```

Você pode aprender tópicos mas avançados em [Ativando alterações em componentes com ações](../../components/triggering-changes-with-actions/), mas antes disso você deve ser familiarizar-se com os seguintes princípios básicos.

## Parâmentros com ações

Você opcionalmente pode passa argumentos de manipulação de ação. Todos os valores passados para o ```{{action }}``` após o nome da ação serão interpretados como argumentos e passados para o manipulador.

Por exemplo, se o argumento ```post``` foi passado:

```html
<p>
  <button {{action 'select' post}}>✓</button>
  {{ post.title }}
</p>
```

Quando o manipulador ```select``` for chamado ele terá um único argumento contendo o modelo de postagem.

```js
import Ember form 'ember';

export default Ember.Component.extend({
  actions: {
    select(post) {
      console.log(post.get('title'));
    }
  }
});
```

## Especificando um tipo de evento

Por padrão, o evento ```{{action }}``` escuta o evento click e aciona a ação quando o usuário clica no elemento.

Você pode especificar um evento alternativo usando a opção ```on```:

```html
<p>
  <button {{action 'select' post on='mouseUp'}}>✓</button>
  {{ post.title }}
</p>
```

Você deve usar a notação de escrita ```camelCased``` para o nome dos eventos, então as palavras que tenham 2 nomes, como ```keypress``` ficará assim ```keyPress```.

## Permitindo chaves de modificação
Por padrão o evento ```{{action}}``` irá ignora os eventos de clique quando uma tecla de modicação forem pressionadas. Você pode fornecer uma opção ```allowedKeys``` para especificar quais teclas não devem ser ignoradas.

```html
<button {{action 'select' post allowedKeys='alt'}}>
  click-me
</button>
```

## Permitindo ação padrão do browser
Por padrão o evento ```{{action}}``` impede a ação padrão do navegador no DOM. Se você quiser permitir qualquer ação do navegador, você pode dizer para o Ember não impedir.

Por exemplo, se você tiver um link normal e quiser um link quer leve o usuário para outra página além de ativar uma ação quando o link for clicado, você pode usar ```preventDefault=false```:

```html
<a href="newPage.html" {{action 'logClick' preventDefault=false}}>Go</a>
```

Com o ```preventDefault=false``` omitido, se o usuário clicar no link, o Ember.js irá ativar ação, mas o usuário irá permancer na página atual.

Com o ```preventDefault=false``` presente, se o usuário clicar no link, o Ember.js irá ativar a ação é o usuário será redirecionando para a nova página.

## Modificando a ação do primeiro parâmentro
Se uma opção de valor for especificada para o ```{{action}}```, seu valor será considerado um caminho de propriedade que será lido no primeiro parâmentro da ação. Isso é útil quando queremos ouvi um evento e permitir trabalhar com ligações unidirecionais.
```html
<label>Qual a sua banda favorita?</label>
<input type="text" name="banda" value="{{favoriteBand}}" onblur="{{bandDidChange}}">
```

Vamos supor que temos um manipulador de ação que irá imprimir no console o primeiro parâmentro:
```js
actions: {
  bandDidChange(newValue) {
    console.log(newValue);
  }
}
```

Por padrão, o manipulador de ação que recebe o primeiro parâmentro ficar escutando os eventos, o navegador passa um objeto de eventos para o manipulador, de modo que o ```bandDidChange``` faz a impressão do ```Evento {}```.

Assim o parâmentro ```newValue``` ser torna uma propriedade ```target.value``` do objeto de evento, sendo assim o valor de entrada que o usuário digitou. (por exemplo 'Foo Fighters')

## Fixando uma ação em elementos não clicável
Observe que as ações podem ser anexadas em qualquer elemento do DOM, mas nem todos os elemento iram responder ao evento click. Por exemplo, se uma ação for anexada a um link sem um atributo href, ou a uma div, alguns navegadores não irão executar a função associada. Ser for realmente necessário definir ações para esses elementos, existe uma solução de CSS para torná-los clicáveis, ```cursor: pointer;```. Por exemplo:

```css
[data-ember-action]:not(disable) {
  cursor: pointer;
}
```

Tenha em mente que, mesmo com essa solução alternativa, o evento de click não será ativado automaticamente por meio de click por teclado (como a tecla ```Enter``` quando focada). Os navegadores irão desencadear isso em elementos clicáveis apenas por padrão. Isso também não torna um elemento acessível aos usuários com tecnologia assertiva. Você precisará adicionar coisas adicionaais como ```role``` e/ou ```tabindex``` para tornar o conteúdo acessível para seus usuários.
