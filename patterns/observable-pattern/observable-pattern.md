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

