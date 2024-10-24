# Clean Code Guide 🌟
> Guia completo para escrever código limpo e manutenível

## Sumário
1. [Princípios Fundamentais](#princípios-fundamentais)
2. [Nomenclatura](#nomenclatura)
3. [Funções](#funções)
4. [Classes e Objetos](#classes-e-objetos)
5. [Tratamento de Erros](#tratamento-de-erros)
6. [Comentários e Documentação](#comentários-e-documentação)
7. [Formatação](#formatação)
8. [Testes](#testes)
9. [SOLID Principles](#solid-principles)
10. [Code Smells](#code-smells)

## Princípios Fundamentais

### DRY (Don't Repeat Yourself)
```java
// Ruim - Código duplicado
void validarUsuario(Usuario usuario) {
    if (usuario.getNome() == null || usuario.getNome().isEmpty()) {
        throw new ValidacaoException("Nome inválido");
    }
    if (usuario.getEmail() == null || usuario.getEmail().isEmpty()) {
        throw new ValidacaoException("Email inválido");
    }
}

// Bom - Função reutilizável
void validarCampo(String valor, String nomeCampo) {
    if (valor == null || valor.isEmpty()) {
        throw new ValidacaoException(nomeCampo + " inválido");
    }
}

void validarUsuario(Usuario usuario) {
    validarCampo(usuario.getNome(), "Nome");
    validarCampo(usuario.getEmail(), "Email");
}
```

### KISS (Keep It Simple, Stupid)
```java
// Complexo demais
boolean isElegivel(Usuario usuario) {
    if (usuario.getIdade() >= 18) {
        if (usuario.getRenda() > 1000) {
            if (!usuario.temRestricoes()) {
                if (usuario.getScore() > 500) {
                    return true;
                }
            }
        }
    }
    return false;
}

// Simples e claro
boolean isElegivel(Usuario usuario) {
    return usuario.getIdade() >= 18
        && usuario.getRenda() > 1000
        && !usuario.temRestricoes()
        && usuario.getScore() > 500;
}
```

## Nomenclatura

### Variáveis
```java
// Ruim
int d; // dias
List<String> lst;
String data; // pode ser data ou dados?

// Bom
int diasDecorridos;
List<String> nomesUsuarios;
LocalDate dataEvento;
```

### Funções/Métodos
```java
// Ruim
void process(); // vago
boolean check(); // o que está verificando?
void userMethod(); // não indica ação

// Bom
void processarPagamento();
boolean isUsuarioAtivo();
void salvarUsuario();
```

### Classes
```java
// Ruim
class Data {}  // muito genérico
class ProcessManager {} // sufixo manager é vago
class UserStuff {} // informal

// Bom
class ProcessamentoPedido {}
class GerenciadorNotificacoes {}
class CalculadoraImpostos {}
```

## Funções

### Regras para Funções Limpas
1. Devem ser pequenas
2. Fazer apenas uma coisa
3. Ter um nível de abstração consistente
4. Poucos parâmetros

```java
// Ruim - Função faz muitas coisas
void processarPedido(Pedido pedido) {
    validarPedido(pedido);
    calcularTotal(pedido);
    aplicarDesconto(pedido);
    salvarNoBanco(pedido);
    enviarEmail(pedido);
    atualizarEstoque(pedido);
}

// Bom - Separado em responsabilidades
class ProcessadorPedido {
    void processar(Pedido pedido) {
        validarPedido(pedido);
        processarPagamento(pedido);
        finalizarPedido(pedido);
    }

    private void validarPedido(Pedido pedido) {
        // Validações específicas
    }

    private void processarPagamento(Pedido pedido) {
        calcularTotal(pedido);
        aplicarDesconto(pedido);
        realizarCobranca(pedido);
    }

    private void finalizarPedido(Pedido pedido) {
        salvarPedido(pedido);
        notificarCliente(pedido);
        atualizarEstoque(pedido);
    }
}
```

## Classes e Objetos

### Coesão e Encapsulamento
```java
// Ruim - Classe sem coesão
class Usuario {
    private String nome;
    private String email;
    private CarrinhoCompras carrinho;
    
    public void processarPagamento() { /* ... */ }
    public void calcularFrete() { /* ... */ }
}

// Bom - Responsabilidades separadas
class Usuario {
    private String nome;
    private String email;
    
    public String getNome() { return nome; }
    public String getEmail() { return email; }
}

class CarrinhoCompras {
    private List<Item> itens;
    private ProcessadorPagamento processador;
    private CalculadoraFrete calculadora;
    
    public void adicionarItem(Item item) { /* ... */ }
    public BigDecimal calcularTotal() { /* ... */ }
}
```

## Tratamento de Erros

### Exceções e Error Handling
```java
// Ruim
public int dividir(int a, int b) {
    if (b != 0) {
        return a / b;
    }
    return -1; // código mágico para erro
}

// Bom
public int dividir(int a, int b) {
    if (b == 0) {
        throw new DivisaoPorZeroException("Divisão por zero não é permitida");
    }
    return a / b;
}

// Tratamento adequado
try {
    int resultado = dividir(10, 0);
} catch (DivisaoPorZeroException e) {
    logger.error("Erro ao realizar divisão: {}", e.getMessage());
    // tratamento específico
}
```

## Comentários e Documentação

### Quando e Como Comentar
```java
// Ruim - Comentário óbvio
// Incrementa contador
contador++;

// Ruim - Código comentado
// void metodoAntigo() {
//     // lógica antiga
// }

// Bom - Explica o porquê
// Taxa de juros é fixa em 0.05 conforme regulamentação bancária
private static final double TAXA_JUROS = 0.05;

// Bom - Documenta API pública
/**
 * Processa o pagamento de um pedido.
 * @param pedido Pedido a ser processado
 * @throws PagamentoException se houver erro na transação
 * @return true se o pagamento foi aprovado
 */
public boolean processarPagamento(Pedido pedido) throws PagamentoException {
    // implementação
}
```

## Formatação

### Padrões de Código
```java
// Organização de Imports
import java.util.List;
import java.util.Map;
import com.empresa.model.*;
import com.empresa.service.*;

// Organização de Classe
public class PedidoService {
    // 1. Constantes
    private static final int MAX_TENTATIVAS = 3;
    
    // 2. Campos
    private final PedidoRepository repository;
    private final NotificacaoService notificacao;
    
    // 3. Construtor
    public PedidoService(PedidoRepository repository,
                        NotificacaoService notificacao) {
        this.repository = repository;
        this.notificacao = notificacao;
    }
    
    // 4. Métodos públicos
    public Pedido criarPedido(/* params */) {
        // implementação
    }
    
    // 5. Métodos privados
    private void validarPedido(/* params */) {
        // implementação
    }
}
```

## Testes

### Princípios de Testes Limpos
```java
// Ruim - Teste confuso e longo
@Test
void test() {
    Usuario u = new Usuario();
    u.setNome("João");
    Pedido p = new Pedido();
    p.setUsuario(u);
    p.adicionar(new Item("Produto", 100));
    service.processar(p);
    assertTrue(p.isProcessado());
}

// Bom - Padrão AAA (Arrange, Act, Assert)
@Test
void deveProcessarPedidoComSucesso() {
    // Arrange
    Usuario usuario = criarUsuarioTeste();
    Pedido pedido = criarPedidoTeste(usuario);
    
    // Act
    service.processar(pedido);
    
    // Assert
    assertTrue(pedido.isProcessado());
    assertEquals(StatusPedido.APROVADO, pedido.getStatus());
}
```

## SOLID Principles

### Single Responsibility Principle
```java
// Ruim - Múltiplas responsabilidades
class Usuario {
    void salvar() { /* lógica de banco */ }
    void enviarEmail() { /* lógica de email */ }
    void gerarRelatorio() { /* lógica de relatório */ }
}

// Bom - Responsabilidades separadas
class Usuario {
    private String nome;
    private String email;
}

class UsuarioRepository {
    void salvar(Usuario usuario) { /* ... */ }
}

class EmailService {
    void enviarEmail(String destinatario, String mensagem) { /* ... */ }
}
```

### Open/Closed Principle
```java
// Ruim
class CalculadoraDesconto {
    double calcular(Produto produto) {
        if (produto.tipo == TipoProduto.ELETRONICO) {
            return produto.preco * 0.1;
        } else if (produto.tipo == TipoProduto.VESTUARIO) {
            return produto.preco * 0.05;
        }
        return 0;
    }
}

// Bom
interface Desconto {
    double calcular(Produto produto);
}

class DescontoEletronico implements Desconto {
    public double calcular(Produto produto) {
        return produto.getPreco() * 0.1;
    }
}

class DescontoVestuario implements Desconto {
    public double calcular(Produto produto) {
        return produto.getPreco() * 0.05;
    }
}
```

## Code Smells

### Identificando e Corrigindo Code Smells
```java
// Long Method Smell
void processarPedido(Pedido pedido) {
    // 100+ linhas de código
}

// Solução: Extrair métodos
void processarPedido(Pedido pedido) {
    validarPedido(pedido);
    calcularValores(pedido);
    finalizarPedido(pedido);
}

// Feature Envy Smell
class Pedido {
    private Cliente cliente;
    
    void exibirDadosCliente() {
        System.out.println(cliente.getNome());
        System.out.println(cliente.getEndereco());
        System.out.println(cliente.getTelefone());
    }
}

// Solução: Mover método para classe apropriada
class Cliente {
    void exibirDados() {
        System.out.println(nome);
        System.out.println(endereco);
        System.out.println(telefone);
    }
}
```

## Recursos Adicionais
- [Clean Code - Robert C. Martin](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)
- [Refactoring - Martin Fowler](https://refactoring.com/)
- [The Pragmatic Programmer](https://pragprog.com/titles/tpp20/the-pragmatic-programmer-20th-anniversary-edition/)
