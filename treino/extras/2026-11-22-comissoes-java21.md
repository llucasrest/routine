# Modelar comissões com Java 21

- **Data:** 2026-11-22
- **Semana:** 01
- **Tempo:** 1 h
- **Tipo:** lacuna (adianta a quarta e a quinta da semana 01)
- **Liga com:** [semana 01](../semanas/semana-01.md) · [projeto de portfólio](../projeto.md)

## Objetivo

Ao final, você tem um domínio de comissões modelado com `record`, `sealed interface` e `switch` com pattern matching, compilando e com testes simples. Esse código vira a base do projeto na quinta.

## Roteiro

- [ ] 10 min: ler o resumo abaixo e os JEPs ([395](https://openjdk.org/jeps/395) records, [409](https://openjdk.org/jeps/409) sealed classes, [441](https://openjdk.org/jeps/441) pattern matching para `switch`, [440](https://openjdk.org/jeps/440) record patterns)
- [ ] 10 min: criar um projeto vazio (`mvn archetype` ou um único arquivo com `java Comissao.java`)
- [ ] 25 min: implementar o exercício
- [ ] 10 min: responder as perguntas de fixação sem olhar o código
- [ ] 5 min: escrever a frase de entrevista em inglês

## Conteúdo

### Resumo

- **`record`**: classe de dados imutável. Gera construtor, `equals`, `hashCode` e `toString`. Valide no construtor compacto.
- **`sealed interface`**: lista fechada de implementações (`permits`). O compilador sabe todos os casos.
- **`switch` com pattern matching**: casa por tipo e desmonta o `record` na mesma linha. Sobre uma interface `sealed`, o `switch` é **exaustivo**: se faltar um caso, não compila; se surgir um novo tipo, o compilador aponta todos os lugares a ajustar.

### Exercício: regras de comissão

Regras (inventadas, sem relação com empregador):

- `Fixa`: percentual sobre a venda.
- `Escalonada`: percentual muda por faixa de valor da venda.
- `Bonus`: valor fixo se a venda passar de uma meta.

```java
import java.math.BigDecimal;
import java.math.RoundingMode;
import java.util.List;

public class Comissao {

    record Venda(String vendedor, BigDecimal valor) {
        Venda {
            if (valor.signum() < 0) throw new IllegalArgumentException("valor negativo");
        }
    }

    record Faixa(BigDecimal ate, BigDecimal percentual) {}

    sealed interface Regra permits Fixa, Escalonada, Bonus {}
    record Fixa(BigDecimal percentual) implements Regra {}
    record Escalonada(List<Faixa> faixas) implements Regra {}   // faixas em ordem crescente; a última cobre o resto
    record Bonus(BigDecimal meta, BigDecimal valor) implements Regra {}

    static BigDecimal calcular(Regra regra, Venda venda) {
        var bruto = switch (regra) {
            case Fixa(var pct) -> venda.valor().multiply(pct);
            case Escalonada(var faixas) -> faixas.stream()
                    .filter(f -> venda.valor().compareTo(f.ate()) <= 0)
                    .findFirst()
                    .orElse(faixas.getLast())
                    .percentual()
                    .multiply(venda.valor());
            case Bonus(var meta, var valor) ->
                    venda.valor().compareTo(meta) > 0 ? valor : BigDecimal.ZERO;
        };
        return bruto.setScale(2, RoundingMode.HALF_EVEN);
    }

    public static void main(String[] args) {
        var venda = new Venda("ana", new BigDecimal("12000"));
        System.out.println(calcular(new Fixa(new BigDecimal("0.05")), venda));       // 600.00
        System.out.println(calcular(new Bonus(new BigDecimal("10000"), new BigDecimal("500")), venda)); // 500.00
    }
}
```

Para rodar: `java Comissao.java` (Java 21 ou superior).

**Desafios, na ordem:**

1. Escreva você mesmo `Escalonada` com 3 faixas (até 5.000 a 3%, até 10.000 a 5%, acima a 7%) e confira os valores à mão.
2. Adicione uma quarta regra, `Composta(List<Regra> regras)`, que soma as outras. Remova o `case` novo do `switch` e leia a mensagem do compilador. Esse é o ganho do `sealed`.
3. Troque `BigDecimal` por `double` num teste com `0.1 + 0.2` e explique por que dinheiro não usa `double`.

### Perguntas de fixação

1. O que o construtor compacto do `record` `Venda` garante, e por que isso importa para um domínio financeiro?
2. Por que o `switch` acima não precisa de `default`? O que acontece se alguém adicionar uma regra nova?
3. Quando um `record` **não** é a escolha certa (por exemplo, entidade JPA com identidade e estado mutável)?
4. Qual a diferença entre `HALF_EVEN` e `HALF_UP`, e qual você usaria em comissões e por quê?

## Frase de entrevista

> "I model business rules as sealed interfaces of records, so the compiler forces every rule to be handled and adding a new one shows me every place that needs to change."

## Resultado

- O que aprendi:
- O que ficou em aberto:
