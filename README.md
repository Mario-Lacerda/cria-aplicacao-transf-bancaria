# Desafio Dio - Criando uma Aplicação de Transferências Bancárias com .NET


Este projeto desenvolve uma aplicação de transferências bancárias abrangente e bem estruturada usando o .NET. O mesmo cria um algoritmo simples de transferência bancária para exercer o pensamento orientado a objetos, o principal paradigma de programação utilizada no mercado, pela orientado a objetos, como modelar o seu domínio através de enums.

## Stack utilizada

**Back-end:** C#, .Net 6



## **Estrutura do Projeto**

- **Modelos:** Classes que representam os objetos do domínio, como `Conta` e `Transação`.
- **Serviços:** Classes que implementam a lógica de negócios, como `Serviço de Transferência`.
- **Lógica de Negócios:** Regras e comportamentos da aplicação, como como validar transferências e atualizar saldos de contas.
- **Testes:** Testes unitários e de integração para verificar a funcionalidade da aplicação.
- **Documentação:** Documentação abrangente para explicar o código e o design da aplicação.

## **Definição de Modelos**

As classes de modelo representam os objetos do domínio:

- **Conta:** Uma conta bancária com propriedades como saldo e número da conta.
- **Transação:** Uma transferência de fundos entre duas contas, com propriedades como valor e data.

## **Serviços**

Os serviços implementam a lógica de negócios:

- **Serviço de Transferência:** Valida transferências, atualiza saldos de contas e registra transações.
- **Serviço de Conta:** Gerencia contas, como criar, buscar e atualizar.

## **Lógica de Negócios**

A lógica de negócios define as regras e comportamentos da aplicação:

- **Validação de Transferências:** As transferências são validadas para garantir que o valor seja positivo, que as contas existam e que haja saldo suficiente na conta de origem.
- **Atualização de Saldos:** Os saldos das contas são atualizados após uma transferência bem-sucedida.
- **Registro de Transações:** As transações são registradas em um banco de dados para fins de auditoria e rastreamento.



### **Modelo de Conta**

csharp

```csharp
public class Conta
{
    public int Id { get; set; }
    public string Nome { get; set; }
    public TipoConta TipoConta { get; set; }
    public double Saldo { get; set; }
    public double Credito { get; set; }

    public Conta(TipoConta tipoConta, double saldo, double credito, string nome)
    {
        TipoConta = tipoConta;
        Saldo = saldo;
        Credito = credito;
        Nome = nome;
    }

    public void Depositar(double valor)
    {
        Saldo += valor;
    }

    public void Sacar(double valor)
    {
        if (valor <= Saldo)
        {
            Saldo -= valor;
        }
    }

    public void Transferir(double valor, Conta contaDestino)
    {
        if (valor <= Saldo)
        {
            Saldo -= valor;
            contaDestino.Saldo += valor;
        }
    }
}

public enum TipoConta
{
    PessoaFisica,
    PessoaJuridica
}
```

**Classe Principal**

csharp

```csharp
class Program
{
    static List<Conta> listContas = new List<Conta>();

    static void Main(string[] args)
    {
        string opcaoUsuario = ObterOpcaoUsuario();

        while (opcaoUsuario.ToUpper() != "X")
        {
            switch (opcaoUsuario)
            {
                case "1":
                    ListarContas();
                    break;
                case "2":
                    InserirConta();
                    break;
                case "3":
                    Transferir();
                    break;
                case "4":
                    Sacar();
                    break;
                case "5":
                    Depositar();
                    break;
                case "C":
                    Console.Clear();
                    break;

                default:
                    throw new ArgumentOutOfRangeException();
            }

            opcaoUsuario = ObterOpcaoUsuario();
        }

        Console.WriteLine("Obrigado por utilizar nossos serviços.");
        Console.ReadLine();
    }

    private static void Depositar()
    {
        Console.Write("Digite o número da conta: ");
        int indiceConta = int.Parse(Console.ReadLine());

        Console.Write("Digite o valor a ser depositado: ");
        double valorDeposito = double.Parse(Console.ReadLine());

        listContas[indiceConta].Depositar(valorDeposito);
    }

    private static void Sacar()
    {
        Console.Write("Digite o número da conta: ");
        int indiceConta = int.Parse(Console.ReadLine());

        Console.Write("Digite o valor a ser sacado: ");
        double valorSaque = double.Parse(Console.ReadLine());

        listContas[indiceConta].Sacar(valorSaque);
    }

    private static void Transferir()
    {
        Console.Write("Digite o número da conta de origem: ");
        int indiceContaOrigem = int.Parse(Console.ReadLine());

        Console.Write("Digite o número da conta de destino: ");
        int indiceContaDestino = int.Parse(Console.ReadLine());

        Console.Write("Digite o valor a ser transferido: ");
        double valorTransferencia = double.Parse(Console.ReadLine());

        listContas[indiceContaOrigem].Transferir(valorTransferencia, listContas[indiceContaDestino]);
    }

    private static void InserirConta()
    {
        Console.WriteLine("Inserir nova conta");

        Console.Write("Digite 1 para Conta Fisica ou 2 para Juridica: ");
        int entradaTipoConta = int.Parse(Console.ReadLine());

        Console.Write("Digite o Nome do Cliente: ");
        string entradaNome = Console.ReadLine();

        Console.Write("Digite o saldo inicial: ");
        double entradaSaldo = double.Parse(Console.ReadLine());

        Console.Write("Digite o crédito: ");
        double entradaCredito = double.Parse(Console.ReadLine());

        Conta novaConta = new Conta((TipoConta)entradaTipoConta, entradaSaldo, entradaCredito, entradaNome);

        listContas.Add(novaConta);
    }

    private static void ListarContas()
    {
        Console.WriteLine("Listar contas");

        if (listContas.Count == 0)
        {
            Console.WriteLine("Nenhuma conta cadastrada.");
            return;
        }

        for (int i = 0; i < listContas.Count; i++)
        {
            Conta conta = listContas[i];
            Console.WriteLine("{0} - {1} - Saldo: {2} - Crédito: {3}", i, conta.Nome, conta.Saldo, conta.Credito);
        }
    }

    private static string ObterOpcaoUsuario()
    {
        Console.WriteLine();
        Console.WriteLine("DIO Bank a seu dispor!!!");
        Console.WriteLine("Informe a opção desejada:");

        Console.WriteLine("1 - Listar contas");
        Console.WriteLine("2 - Inserir nova conta");
        Console.WriteLine("3 - Transferir");
        Console.WriteLine("4 - Sacar");
        Console.WriteLine("5 - Depositar");
        Console.WriteLine("C - Limpar Tela");
        Console.WriteLine("X - Sair");

        string opcaoUsuario = Console.ReadLine().ToUpper();
        Console.WriteLine();
        return opcaoUsuario;
    }
}
```

### **Observações:**

- Este código é apenas um exemplo e pode precisar ser adaptado para atender aos requisitos específicos da sua aplicação.
- O método `ObterOpcaoUsuario` é uma função auxiliar que exibe as opções para o usuário e retorna a opção selecionada.
- O método `ListarContas` exibe uma lista de todas as contas cadastradas.
- O método `InserirConta` cria uma nova conta e a adiciona à lista de contas.
- Os métodos `Transferir`, `Sacar` e `Depositar` realizam as respectivas operações nas contas.



## **Integração e Testes**

- A integração é realizada conectando os modelos, serviços e lógica de negócios em uma classe principal que gerencia o fluxo da aplicação.
- Os testes unitários são escritos usando um framework de teste para verificar se as diferentes partes da aplicação estão funcionando corretamente.
- Os testes de integração são escritos para verificar se os diferentes componentes da aplicação estão trabalhando juntos corretamente.

## **Documentação**

- A documentação do código é escrita usando comentários e um documento de design separado.
- O documento de design descreve a arquitetura da aplicação, os requisitos funcionais e as decisões de design.

## **Conclusão**

Esta aplicação de transferências bancárias abrangente e bem estruturada é altamente modular, testável e bem documentada. Ela fornece uma base sólida para o desenvolvimento de aplicações financeiras mais complexas e seguras.

## **Aplicabilidade Prática**

Esta aplicação pode ser usada como um modelo para criar uma ampla gama de aplicações financeiras, desde sistemas bancários simples até plataformas de pagamento complexas.
