# Implementando o Padrão Strategy com Exemplo Prático

## Contexto: O Problema da Devolução de Carros

Imagine uma locadora de carros que possui duas regras diferentes para devolução de veículos:

- O cliente só pode devolver o carro no local onde o retirou.
- O cliente pode devolver o carro em qualquer local da rede de locadoras.

Inicialmente, o sistema implementa apenas a primeira regra. Posteriormente, surge a necessidade de adicionar a segunda opção sem modificar o código existente de forma significativa. Como resolver isso?

Aqui entra o padrão Strategy.

## O Que é o Strategy?

O **Strategy** é um padrão comportamental que permite definir uma família de algoritmos, encapsulá-los em classes separadas e torná-los intercambiáveis. Ele ajuda a tornar o sistema flexível e aderente ao Open-Closed Principle (princípio aberto-fechado), que sugere que o código deve ser aberto para extensão, mas fechado para modificação.

No caso da locadora de carros, cada regra de validação de devolução será representada como uma "estratégia" separada.

## Implementação Inicial: Apenas Devolução no Local de Origem

Inicialmente, implementamos a regra onde o cliente só pode devolver o carro no local onde o retirou.

### Classes Envolvidas

```java
public class Carro {
    private String modelo;

    public Carro(String modelo) {
        this.modelo = modelo;
    }

    public String getModelo() {
        return modelo;
    }
}

public class LocalAluguel {
    private String nome;

    public LocalAluguel(String nome) {
        this.nome = nome;
    }

    public String getNome() {
        return nome;
    }
}

public interface ValidadorDevolucao {
    boolean validarDevolucao(Carro carro, LocalAluguel localOrigem, LocalAluguel localDevolucao);
}
```
## Primeira Implementação da Estratégia

```
public class ValidadorDevolucaoOrigem implements ValidadorDevolucao {
    @Override
    public boolean validarDevolucao(Carro carro, LocalAluguel localOrigem, LocalAluguel localDevolucao) {
        return localOrigem.getNome().equals(localDevolucao.getNome());
    }
}
```

### Serviço de Aluguel

```
public class ServicoAluguel {
    private LocalAluguel localOrigem;
    private ValidadorDevolucao validadorDevolucao;

    public ServicoAluguel(LocalAluguel localOrigem, ValidadorDevolucao validadorDevolucao) {
        this.localOrigem = localOrigem;
        this.validadorDevolucao = validadorDevolucao;
    }

    public boolean devolverCarro(Carro carro, LocalAluguel localDevolucao) {
        return validadorDevolucao.validarDevolucao(carro, localOrigem, localDevolucao);
    }
}
``
### Uso do Sistema

```public class Main {
    public static void main(String[] args) {
        LocalAluguel localOrigem = new LocalAluguel("Locadora A");
        ValidadorDevolucao validador = new ValidadorDevolucaoOrigem();

        ServicoAluguel servico = new ServicoAluguel(localOrigem, validador);

        Carro carro = new Carro("Sedan");
        LocalAluguel localDevolucao = new LocalAluguel("Locadora A");

        boolean podeDevolver = servico.devolverCarro(carro, localDevolucao);
        System.out.println("Pode devolver: " + podeDevolver);
    }
}
``` 
### Adicionando a Opção de Devolução em Qualquer Local

Agora surge a necessidade de permitir que o cliente devolva o carro em qualquer local da rede. Em vez de modificar o código existente, criamos uma nova implementação da interface ValidadorDevolucao.
Nova Implementação

``public class ValidadorDevolucaoQualquerLocal implements ValidadorDevolucao {
    @Override
    public boolean validarDevolucao(Carro carro, LocalAluguel localOrigem, LocalAluguel localDevolucao) {
        return true; // Permite devolução em qualquer local
    }
}``

Alterando o Uso do Sistema

```
public class Main {
    public static void main(String[] args) {
        LocalAluguel localOrigem = new LocalAluguel("Locadora A");
        ValidadorDevolucao validador = new ValidadorDevolucaoQualquerLocal(); // Mudança aqui

        ServicoAluguel servico = new ServicoAluguel(localOrigem, validador);

        Carro carro = new Carro("SUV");
        LocalAluguel localDevolucao = new LocalAluguel("Locadora B");

        boolean podeDevolver = servico.devolverCarro(carro, localDevolucao);
        System.out.println("Pode devolver: " + podeDevolver);
    }
}
``
## Por que o Strategy?

O padrão **Strategy** foi escolhido porque:

- **Encapsula o comportamento variável**: As regras de validação de devolução são separadas em classes diferentes, tornando o sistema modular.
- **Facilita a extensibilidade**: É fácil adicionar novas regras de validação sem modificar o código existente.
- **Aumenta a manutenção**: Cada regra tem seu próprio escopo, reduzindo o risco de erros ao fazer alterações.

## Quando Usar Padrões de Criação?

No exemplo, as instâncias de `ValidadorDevolucao` foram criadas manualmente. Em cenários mais complexos, padrões de criação como **Factory** ou **Builder** poderiam ser usados para:

- Decidir qual estratégia usar dinamicamente com base em configurações ou contexto.
- Gerenciar dependências externas ao criar os validadores.
- Garantir a reutilização ou otimização de instâncias.

## Conclusão

O padrão **Strategy** é uma solução poderosa para lidar com comportamentos variáveis em sistemas.

No exemplo da locadora de carros, ele permitiu separar as regras de validação de devolução em classes distintas, tornando o código mais flexível e aderente aos princípios de design orientado a objetos.

Quando combinado com outros padrões, como os de criação, o **Strategy** pode criar soluções ainda mais robustas e escaláveis.

## Visite meu blog em: https://www.jcsalerno.com.br/blog/2025/01/13/entendendo-o-padrao-strategy-com-um-exemplo-de-locadora-de-carros/
