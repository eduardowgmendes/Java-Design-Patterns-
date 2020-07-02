# Padrão Observer 

### Definição 
Estabelece uma dependência do tipo `um-para-muitos` entre os objetos onde alguns objetos observam outro objeto em particular e quando esse objeto muda seu estado interno, todos os seus dependentes são notificados e atualizados automaticamente.

### Conceito 
Nesse padrão de projeto existem muitos objetos observadores chamados de `observers` que estão observando um outro objeto em particular chamado de `subject`. Os observers basicamente estão interessados na mudança do estado interno desse objeto e querem ser notificados quando ele mudar. Então eles se registram ao subject. Os observers podem a qualquer momento se desligarem do objeto subject quando não há mais a necessidade de observá-lo. Este modelo de design também pode ser chamado de `Publisher-Subscriber`.

### Exemplo da Vida Real
Nós podemos ilustrar esse padrão pensando em uma celebridade que possui muitos fãs. Cada um desses fãs querem saber todas as últimas notícias e atualizações de sua celebridade favorita. Então eles podem seguí-la por um determinado período de tempo enquanto o interesse persistir. Quando eles perdem o interesse, eles simplesmente deixam de seguir essa celebridade. Podemos dizer que o fã é um observer e a celebridade seria o subject.

### Exemplo Computacional
Trazendo esse cenário para o desenvolvimento de software, considere por exemplo uma aplicação qualquer que possui uma interface gráfica e essa aplicação está conectada a um banco de dados (ou a alguma lógica de negócio). Um usuário pode executar alguma query através da interface gráfica como em um campo de pesquisa por exemplo e a aplicação busca no banco de dados os resultados e reflete de volta esses dados para o usuário na interface gráfica. As duas camadas da aplicação são obviamente são separadas e quando uma mudança ocorre na base de dados, a interface gráfica deve ser notificada e atualizada exibindo essa mudança.   

### Observer.java
```java
public class Observer {
    public void update() {
        System.out.println("Flag value changed in Subject");
    }
}

```
### ISubject.java
```java
public interface ISubject {
    void register(Observer o);

    void unregister(Observer o);

    void notifyObservers();
}
```

### Subject.java
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
