# Padrão Observer 

### Definição 
Estabelece uma dependência do tipo `um-para-muitos` entre os objetos onde alguns objetos observam outro objeto em particular e quando esse objeto muda seu estado interno, todos os seus dependentes são notificados e atualizados automaticamente.

### Conceito 
Nesse padrão de projeto existem muitos objetos observadores chamados de `observers` que estão observando um outro objeto em particular chamado de `subject`. Os observers basicamente estão interessados na mudança do estado interno desse objeto e querem ser notificados quando ele mudar. Então eles se registram ao subject. Os observers podem a qualquer momento se desligarem do objeto subject quando não há mais a necessidade de observá-lo. Este modelo de design também pode ser chamado de `Publisher-Subscriber`.

### Exemplo da Vida Real
Nós podemos ilustrar esse padrão pensando em uma celebridade que possui muitos fãs. Cada um desses fãs querem saber todas as últimas notícias e atualizações de sua celebridade favorita. Então eles podem seguí-la por um determinado período de tempo enquanto o interesse persistir. Quando eles perdem o interesse, eles simplesmente deixam de seguir essa celebridade. Podemos dizer que o fã é um observer e a celebridade seria o subject.

### Exemplo Computacional
Trazendo esse cenário para o desenvolvimento de software, considere por exemplo uma aplicação qualquer que possui uma interface gráfica e essa aplicação está conectada a um banco de dados (ou a alguma lógica de negócio). Um usuário pode executar alguma query através da interface gráfica como em um campo de pesquisa por exemplo e a aplicação busca no banco de dados os resultados e reflete de volta esses dados para o usuário na interface gráfica. As duas camadas da aplicação são obviamente são separadas e quando uma mudança ocorre na base de dados, a interface gráfica deve ser notificada e atualizada exibindo essa mudança.  

### Exemplo para compreensão

Vamos compreender em um exemplo prático como funciona esse padrão de projeto. Aqui nós criaremos um observer (você poderia criar mais do que um) e um subject. O subject mantém uma lista de todos os `observers` (neste exemplo temos apenas um apenas para simplificar). Nosso observer quer ser notificado quando o valor de um atributo do `subject` chamado `flag` mudar. Analisando a saída desse exemplo você perceberá que o observer realmente é notificado quando o valor é modificado no `subject` para 5 ou 25. Mas não há nenhuma notificação quando o valor é modificado para 50 porque dessa vez o `observer` foi desligado do `subject` na chamada ao método `void unregister(Observer o)`.     

### Observer.java
Este é o Observer que será notificado quando houver mudanças no estado interno da classe Subject 
```java
public class Observer {
    public void update() {
        System.out.println("Flag value changed in Subject");
    }
}

```
### ISubject.java
Interface que contém os métodos para registrar, remover e notificar objetos `observer`:

* `void register(Observer o)` - aqui não há implementação mas esse método será usado para adicionar um observer à lista de observers na classe `Subject`. 
 
* `void unregister(Observer o)` - aqui não há implementação mas esse método será usado para remover um observer da lista de observers na classe `Subject`.

* `void notifyObservers()` - aqui não há implementação mas esse método será usado para notificar um observer registrado quando houver mudança do estado interno do `subject`

```java
public interface ISubject {
    void register(Observer o);

    void unregister(Observer o);

    void notifyObservers();
}
```

### Subject.java
Classe que será nosso Subject nesse exemplo. Aqui ela implementa a interface `ISubject` com os métodos citados acima. Note que temos uma lista com todos os `observers` além do atributo `flag` que modificaremos durante o exemplo.
```java
public class Subject implements ISubject {

    private List<Observer> observerList = new ArrayList<>();
    private int flag;

    public int getFlag() {
        return flag;
    }

    public void setFlag(int flag) {
        this.flag = flag;
        // Flag value changed. So notify observers
        notifyObservers();
    }

    @Override
    public void register(Observer o) {
        observerList.add(o);
    }

    @Override
    public void unregister(Observer o) {
        observerList.remove(o);
    }

    @Override
    public void notifyObservers() {
        for (int i = 0; i < observerList.size(); i++) {
            observerList.get(i).update();
        }
    }
}
```

### ObserverExample.java
Classe principal onde testaremos o exemplo. Note que logo após a chamada ao método `void unregister(Observer o)` o `observer` não é mais notificado sobre a mudança do estado interno de `subject` representado sobre o atributo `flag` que modificaremos para o valor 50. 
```java
public class ObserverExample {

    public static void main(String[] args) {
        System.out.println("***Observer Patterns Demo***");
        Observer observer = new Observer();
        Subject subject = new Subject();

        subject.register(observer);
        System.out.println("Setting flag: 5");
        subject.setFlag(5);
        System.out.println("Setting flag: 25");
        subject.setFlag(25);
        subject.unregister(observer);

        // No notification this time to observer. Since it is unregistered.
        System.out.println("Setting flag: 50");
        subject.setFlag(50);
    }
}
```

### Saída no Console 
```
Setting flag: 5
Flag value changed in Subject
Setting flag: 25
Flag value changed in Subject
Setting flag: 50

Process finished with exit code 0
```

Vamos deixar o exemplo anterior um pouco mais interessante. O exemplo acima ilustra de forma simples a estrutura desse padrão. Agora vamos considerar um exemplo um pouco mais complexo. Vamos assumir o seguinte: 

* Agora nós precisamos ter em nosso projeto múltiplos `Observers`.
* E nós também queremos saber sobre a mudança exata no `Subject`. Se você analisar nossa implementação anterior, você pode facilmente entender que nós tínhamos algum tipo de notificação mas nossos observers não sabiam sobre a mudança de valores em nosso subject.

Obviamente nós teremos que fazer algumas mudanças em nosso design e ela começa com o fato de que agora não teremos mais uma lista de observers concretos dessa forma:

```java
List<Observer> observerList = new ArrayList<>();
```
Ao invés disso teremos: 

```java
List<IObserver> observerList = new ArrayList<>();
```
E também precisaremos substituir a classe concreta Observer com a interface IObserver em vários lugares correspondentes para atendermos o polimorfismo. Também modificaremos o método update para exibir os valores nos observers. Vamos lá então!

### IObserver.java 
Agora teremos uma interface chamada IObserver que traz o método `void update(int value)` que será implementado por todos os observers.
```java
public interface IObserver {

    void update(int v);
}
```
### Observer.java 
Agora para ser um observer e poder ser registrado na lista de observers toda classe que quiser alcançar esse objetivo terá que implementar a interface `IObserver` e consequentemente seu único método `void update(int value)`. 
```java
public class Observer implements IObserver {

    @Override
    public void update(int v) {
        System.out.println("Observer: myValue in Subject is now: " + v);
    }
}
```

### AnotherObserver.java 
Assim como a classe acima `AnotherObserver` também implementa a interface `IObserver`. 
```java
public class AnotherObserver implements IObserver {
    @Override
    public void update(int v) {
        System.out.println("Another Observer: observes -> myValue is changed in Subject to: " + v);
    }
}

```

### ISubject.java
Dessa vez `ISubject` modifica a assinatura de seus métodos para `void register(IObserver o)`, `void unregister(IObserver o)`, `void notifyObservers(int value)`.
```java
public interface ISubject {
    void register(IObserver o);

    void unregister(IObserver o);

    void notifyObservers(int i);
}

```

### Subject.java 
Note que agora na classe `Subject` modificamos os tipos `Observer` para `IObserver` em alguns locais correspondentes para atender a nova implementação de `ISubject`.
```java
public class Subject implements ISubject {

    private List<IObserver> observerList = new ArrayList<>();
    private int myValue;

    public int getMyValue() {
        return myValue;
    }

    public void setMyValue(int myValue) {
        this.myValue = myValue;
        // Flag value changed. So notify observers
        notifyObservers(myValue);
    }

    @Override
    public void register(IObserver o) {
        observerList.add(o);
    }

    @Override
    public void unregister(IObserver o) {
        observerList.remove(o);
    }

    @Override
    public void notifyObservers(int v) {
        for (int i = 0; i < observerList.size(); i++) {
            observerList.get(i).update(v);
        }
    }
}
```

### ObserverExample.java 
O mais interessante é que agora se você notar a chamada do método `void unregister(IObserver o)` nós estamos desligando apenas o `observer1` e apenas ele não será mais notificado das mudanças que ocorrem no subject.
```java
public class ObserverExample {

    public static void main(String[] args) {
        System.out.println("***Observer Patterns Demo***");
        IObserver observer1 = new Observer();
        IObserver observer2 = new AnotherObserver();
        Subject subject = new Subject();

        subject.register(observer1);
        subject.register(observer2);

        System.out.println("Setting flag: 5");
        subject.setMyValue(5);
        System.out.println("Setting flag: 25");
        subject.setMyValue(25);

        // Unregistering only observer 1
        subject.unregister(observer1);

        // Now only observer2 will observe the change
        System.out.println("Setting flag: 100");
        subject.setMyValue(100);
    }
}

```

### Saída no Console 
```
***Observer Patterns Demo***
Setting flag: 5
Observer: myValue in Subject is now: 5
Another Observer: observes -> myValue is changed in Subject to: 5
Setting flag: 25
Observer: myValue in Subject is now: 25
Another Observer: observes -> myValue is changed in Subject to: 25
Setting flag: 100
Another Observer: observes -> myValue is changed in Subject to: 100

Process finished with exit code 0
```

