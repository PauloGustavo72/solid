# solid

### Single Responsibility Principle (S)
Esse princípio diz que nossas classes devem ter responsablidades únicas e somente um motivo para mudar. Nossa classe dever ser *coesa*.
Exemplo de uma classe **ruim** que pode ser aplicado esse padrão: </br>

```java
public class CalculadoraDeSalario {


    public double calcula(Funcionario funcionario) {
        if(DESENVOLVEDOR.equals(funcionario.getCargo())) {
            return dezOuVintePorcento(funcionario);
        }

        if(DBA.equals(funcionario.getCargo()) || TESTER.equals(funcionario.getCargo())) {
            return quinzeOuVinteCincoPorcento(funcionario);
        }

        throw new RuntimeException("funcionario invalido");
    }

    private double dezOuVintePorcento(Funcionario funcionario) {
        if(funcionario.getSalarioBase() > 3000.0) {
            return funcionario.getSalarioBase() * 0.8;
        }
        else {
            return funcionario.getSalarioBase() * 0.9;
        }
    }

    private double quinzeOuVinteCincoPorcento(Funcionario funcionario) {
        if(funcionario.getSalarioBase() > 2000.0) {
            return funcionario.getSalarioBase() * 0.75;
        }
        else {
            return funcionario.getSalarioBase() * 0.85;
        }
    }

}
```
Uma possível solução para esse problema seria criar uma interface(`RegraDeCalculo`) que possua o método `calcula(Funcionario funcionario)` e duas outras classe que implementam ela como mostrado abaixo:
``` java
public class DezOuVintePorCento implements RegraDeCalculo {

    public double calcula(Funcionario funcionário);
            if(funcionario.getSalarioBase() > 3000.0) {
                return funcionario.getSalarioBase() * 0.8;
        }
        else {
            return funcionario.getSalarioBase() * 0.9;
        }
 }
 
 public class QuinzeOuVinteECincoPorCento implements RegraDeCalculo  {
    public double calcula(Funcionario funcionario) {
        if(funcionario.getSalarioBase() > 2000.0) {
            return funcionario.getSalarioBase() * 0.75;
        }
        else {
            return funcionario.getSalarioBase() * 0.85;
        }
    }

}
 ```
 Assim quebramos as responsabilidades e essas novas classes só possui uma única responsabilidade e somente um motivo para mudar(caso mude a regra de quinzeOuVintePorCento, ou DezOuVintePorCento).
 
 Devemos também passar um funcionário como parâmetro no *Enum* (Cargo), conforme a imagem abaixo:
 ``` java
 public enum Cargo {
    DESENVOLVEDOR(new DezOuVintePorCento()),
    DBA(new DezOuVintePorCento()),
    TESTER(new QuinzeOuVinteECincoPorCento());

    Cargo(RegraDeCalculo regra)  {
        this.regra = regra;
    }
    public RegraDeCalculo getRegra()  {
        return regra;
    }
}
```

Com isso obrigamos o desenvolvedor a cada novo cargo criado, passar como argumento um tipo de cálculo. A classe que calcula o salário baseado na regra fica da seguinte forma:
``` java
public double calcula(Funcionario funcionario)  {
    return funcionario.getCargo().getRegra().calcula(funcionario);`
}
```
*Ou simplificando mais ainda:*
``` java
public double  calcula(Funcionario funcionario)  {
     return funcionario.calculaSalario();
}
```
*E o método calculaSalario na classe Funcionario assim:*
``` java
public double calculaSalario()  {
    return  cargo.getRegra().calcula(this);
}
```
