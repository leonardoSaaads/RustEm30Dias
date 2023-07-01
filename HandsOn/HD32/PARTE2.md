# Continuação - Lidando com Arquivos

Para deixarmos nosso projeto mais estruturado, vamos extrair uma função chamada ``run`` que manterá toda a lógica atual na função principal que não está envolvida na definição de configuração ou tratamento de erros. Quando terminarmos, ``main`` será conciso e fácil de verificar por inspeção, e poderemos escrever testes para todas as outras lógicas. Por enquanto, estamos apenas fazendo uma pequena melhoria incremental de extrair a função. Ainda estamos definindo a função em src/main.rs.

```
use std::env;
use std::fs;
use std::process;

fn main() {
    let args: Vec<String> = env::args().collect();

    let config = Config::construtor(&args).unwrap_or_else(|err| {
        eprintln!("Erro ao analisar os argumentos: {}", err);
        process::exit(1);
    });

    println!("Procurando por:\n{}", config.query);
    println!("No arquivo:\n{}", config.file_path);

    run(config);
}

fn run(config: Config) {
    let contents = fs::read_to_string(&config.file_path).unwrap_or_else(|err| {
        eprintln!("Não foi possível abrir o arquivo: {}", err);
        process::exit(1);
    });

    println!("Com o texto:\n{}", contents);

    // --snip--
}

struct Config {
    query: String,
    file_path: String,
}

impl Config {
    fn construtor(args: &[String]) -> Result<Config, &'static str> {
        if args.len() < 3 {
            return Err("Não há elementos suficientes");
        }

        let query = args.get(1).unwrap().to_owned();
        let file_path = args.get(2).unwrap().to_owned();

        Ok(Config { query, file_path })
    }
}

```

Mas veja que podemos fazer justamente o mesmo processo de ``construtor`` com a função ``run``. Além disso, muitos usuários optam por utilizar ``fn run(config: Config) -> Result<(), Box<dyn Error>>`` na função ``run`` tendo em vista que podemos lidar com erros e exibirmos as mensagens necessárias. Veja a sua implementação:

```
use std::env;
use std::error::Error;
use std::fs;
use std::process;

fn main() {
    let args: Vec<String> = env::args().collect();

    let config = Config::build(&args).unwrap_or_else(|err| {
        eprintln!("Erro ao analisar os argumentos: {}", err);
        process::exit(1);
    });

    println!("Procurando por:\n{}", config.query);
    println!("No arquivo:\n{}", config.file_path);

    if let Err(e) = run(config) {
        eprintln!("Erro ao executar a aplicação: {}", e);
        process::exit(1);
    }
}

fn run(config: Config) -> Result<(), Box<dyn Error>> {
    let contents = fs::read_to_string(&config.file_path)?;

    println!("Com o texto:\n{}", contents);

    // --snip--

    Ok(())
}

pub struct Config {
    pub query: String,
    pub file_path: String,
}

impl Config {
    pub fn build(args: &[String]) -> Result<Config, &'static str> {
        if args.len() < 3 {
            return Err("Não há elementos suficientes");
        }

        let query = args.get(1).unwrap().to_owned();
        let file_path = args.get(2).unwrap().to_owned();

        Ok(Config { query, file_path })
    }
}

```

Agora, a função ``run`` retorna um ``Result`` e a função ``main`` lida com possíveis erros retornados por ela. Além disso, a estrutura ``Config`` e sua implementação foram atualizadas. Todavia, vale ressaltar que nosso código tem um programa fundamental! Ele não está realizando testes de verificação para averiguar se o código está bem estruturado - e essa é uma etapa fundamental para robustez de todo software que almeja escalabilidade. Nesse sentido, teremos de implementar essa funcionalidade.

A função de teste especifica o comportamento que queremos que a função de pesquisa tenha: ela levará uma consulta e o texto a ser pesquisado e retornará apenas as linhas do texto que contém a consulta. Veja abaixo:

```
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn one_result() {
        let query = "fácil";
        let contents = "\
Rust:
Ruste é uma linguagem de programação performática, simples,
ágil, dinâmica e difícil de aprender, correto?!?!";

        assert_eq!(vec!["safe, fast, productive."], pesquisa(query, contents));
    }
}
```

Ainda não podemos executar este teste e vê-lo falhar porque o teste nem compila: a função de ``pesquisa`` ainda não existe! Além disso, vamos colocar a parte de erro no código: Veja um exemplo abaixo:

```
use std::env;
use std::error::Error;
use std::fs;
use std::process;

fn main() {
    let args: Vec<String> = env::args().collect();

    let config = Config::build(&args).unwrap_or_else(|err| {
        eprintln!("Erro ao analisar os argumentos: {}", err);
        process::exit(1);
    });

    println!("Procurando por:\n{}", config.query);
    println!("No arquivo:\n{}", config.file_path);

    if let Err(e) = run(config) {
        eprintln!("Erro ao executar a aplicação: {}", e);
        process::exit(1);
    }
}

fn run(config: Config) -> Result<(), Box<dyn Error>> {
    let contents = fs::read_to_string(&config.file_path)?;
    for line in pesquisa(&config.query, &contents) {
        println!("{}", line);
    }
    Ok(())
}

pub fn pesquisa<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    contents
        .lines()
        .filter(|line| line.contains(query))
        .collect()
}

pub struct Config {
    pub query: String,
    pub file_path: String,
}

impl Config {
    pub fn build(args: &[String]) -> Result<Config, &'static str> {
        if args.len() < 3 {
            return Err("Não há elementos suficientes");
        }
        let query = args.get(1).unwrap().to_owned();
        let file_path = args.get(2).unwrap().to_owned();
        Ok(Config { query, file_path })
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn one_result() {
        let query = "fácil";
        let contents = "\
Rust:
Rust é uma linguagem de programação performática, simples,
ágil, dinâmica e fácil de aprender, correto?!?!";

        assert_eq!(vec!["ágil, dinâmica e fácil de aprender, correto?!?!"], pesquisa(query, contents));
    }
}
```

A função ``pesquisa`` recebe como parâmetros uma query e o conteúdo de um arquivo em forma de ``string``. Ela retorna um vetor com todas as linhas do arquivo que contêm a ``query``. A função ``run`` foi atualizada para chamar a função pesquisa e imprimir as linhas encontradas.

Para finalizarmos esse projeto, iremos apenas adicionarpequenas modificações no código, mas que são importantes para aferir se ele está funcionando corretamente. Adicionamos comentários, ``Config::build`` retorna uma mensagem de erro mais descritiva e oram adicionados testes para a função Config::build para verificar se ela está funcionando corretamente. 

```
use std::env;
use std::error::Error;
use std::fs;
use std::process;

fn main() {
    let args: Vec<String> = env::args().collect();

    let config = Config::build(&args).unwrap_or_else(|err| {
        eprintln!("Erro ao analisar os argumentos: {}", err);
        process::exit(1);
    });

    println!("Procurando por:\n{}", config.query);
    println!("No arquivo:\n{}", config.file_path);

    if let Err(e) = run(config) {
        eprintln!("Erro ao executar a aplicação: {}", e);
        process::exit(1);
    }
}

fn run(config: Config) -> Result<(), Box<dyn Error>> {
    let contents = fs::read_to_string(&config.file_path)?;
    for line in pesquisa(&config.query, &contents) {
        println!("{}", line);
    }
    Ok(())
}

/// Retorna todas as linhas do `contents` que contêm a `query`.
pub fn pesquisa<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    contents
        .lines()
        .filter(|line| line.contains(query))
        .collect()
}

pub struct Config {
    pub query: String,
    pub file_path: String,
}

impl Config {
    /// Constrói uma nova instância de `Config` a partir dos argumentos da linha de comando.
    ///
    /// Retorna um erro se não houver argumentos suficientes.
    pub fn build(args: &[String]) -> Result<Config, String> {
        if args.len() < 3 {
            return Err(format!(
                "número insuficiente de argumentos: esperava 2, recebeu {}",
                args.len() - 1
            ));
        }
        let query = args.get(1).unwrap().to_owned();
        let file_path = args.get(2).unwrap().to_owned();
        Ok(Config { query, file_path })
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn one_result() {
        let query = "fácil";
        let contents = "\
Rust:
Rust é uma linguagem de programação performática, simples,
ágil, dinâmica e fácil de aprender, correto?!?!";

        assert_eq!(vec!["ágil, dinâmica e fácil de aprender, correto?!?!"], pesquisa(query, contents));
    }

    #[test]
    fn config_build_success() {
        let args = vec![
            "program_name".to_string(),
            "query".to_string(),
            "file_path".to_string(),
        ];
        let config = Config::build(&args).unwrap();
        assert_eq!(config.query, "query");
        assert_eq!(config.file_path, "file_path");
    }

    #[test]
    fn config_build_failure() {
        let args = vec!["program_name".to_string()];
        let result = Config::build(&args);
        assert!(result.is_err());
        assert_eq!(
            result.unwrap_err(),
            "número insuficiente de argumentos: esperava 2, recebeu 0"
        );
    }
}
```

Se executarmos o comando para acharmos a palavra **inclinando** no poema de Machado de Assis, teremos a seguinte resposta:

```
cargo run -- inclinando poema.txt 
   Compiling ler_arquivos v0.1.0 (C:...\ler_arquivos)
    Finished dev [unoptimized + debuginfo] target(s) in 14.26s
     Running `target\debug\ler_arquivos.exe inclinando poema.txt`
Procurando por:
inclinando 
No arquivo:
poema.txt
Mas o sol, inclinando a rútila capela:
```

O que mostra que nosso código funcionou corretamente!!! Parábens, pois agora você está apto a entender como alguns arquivos são manipulados e utilizados em Rust. 

Infelizmente nossa jornada terminou! (😔😔)
Mas não fique triste, pois agora você já possui mais conhecimento e aptidão para lidar com essa incrivel linguagem de programação. Fique à vontade para notar como foi o seu crescimento intelectual e profissional foram transformados! O início do caminho fai percorrido, agora você terá de escolher as novas batalhas que serão necessárias lidar.

Fico feliz que tenha chegado até aqui e até a proxima!!

![Agradecimento](/Imagens/HD32/agradecimento.jpg)