# **APRENDENDO RUST - LINGUAGEM DE PROGRAMAÇÃO 5G/6G**

Este repositório é destinado para o aprendizado da linguagem de programação Rust. Rust é uma linguagem de programação multiparadigma compilada desenvolvida pela Mozilla Research. É projetada para ser "segura, concorrente e prática", mas diferente de outras linguagens seguras, Rust não usa coletor de lixo. Possui suporte nativo ao WebAssembly.

## **Tabela de Aprendizado Rust**

<table>
<thead>
  <tr>
    <th> HANDS-ON </th>
    <th> LINK </th>
    <th> NÍVEL </th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>1</td>
    <td><a href="/HandsOn/HD01/">Conhecendo o Rust</a></td>
    <td>ESSENCIAL</td>
  </tr>
  <tr>
    <td>2</td>
    <td><a href="/HandsOn/HD02/">Hello World, Hello Cargo</a></td>
    <td>ESSENCIAL</td>
  </tr>
  <tr>
    <td>3</td>
    <td><a href="/HandsOn/HD03/">Variáveis, Mutabilidade e Data Types</a></td>
    <td>ESSENCIAL</td>
  </tr>
  <tr>
    <td>4</td>
    <td><a href="/HandsOn/HD04/">Funções</a></td>
    <td>ESSENCIAL</td>
  </tr>
  <tr>
    <td>5</td>
    <td><a href="/HandsOn/HD05/">Comentários e Controle de Fluxo</a></td>
    <td>ESSENCIAL</td>
  </tr>
  <tr>
    <td>6</td>
    <td><a href="/HandsOn/HD06/">EXERCICIOS 01</a></td>
    <td>ESSENCIAL</td>
  </tr>
  <tr>
    <td>7</td>
    <td><a href="/HandsOn/HD07/">Ownership</a></td>
    <td>BÁSICO</td>
  </tr>
  <tr>
    <td>8</td>
    <td><a href="/HandsOn/HD08/">Referencias e Empréstimos</a></td>
    <td>BÁSICO</td>
  </tr>
  <tr>
    <td>9</td>
    <td><a href="/HandsOn/HD09/">Slices</a></td>
    <td>BÁSICO</td>
  </tr>
</tbody>
</table>

## **Instalação do Rust (Windows)**

Para realizar o download de Rust, acesse: [Rust Lang Org](https://www.rust-lang.org/tools/install).

![](/Imagens/Instalation/Rust_download_windows.png)

- Clique em "rustup-init.exe (64-bit)", se o seu computador for 64 bits.

- Clique em "rustup-init.exe (32-bit)", se o seu computador for 32 bits.

Execute o arquivo e espere a instalação no seu computador. Para verificar se a linguagem foi corretamente instalada, execute o comando ``rustup --version`` em seu prompt de comando. A tela de execução deverá ser semelhante a tela abaixo:

![](/Imagens/Instalation/Rust_download_windows_terminal.png)

## **Instalação do Rust (Linux)**

Em primeiro lugar, atualize o linux utilzando o comando ``sudo apt-get update`` para distribuições baseadas em Debian.
Em outras distribuições, como a Fedora, o comando será diferente. Nesse caso, o camando para a atualização será ``sudo dnf updade``.

Em segundo lugar, realize o upgrade. Em distribuições baseadas em debian, o comando é ``sudo apt-get upgrade``. Para distribuições Fedora, o comando é ``sudo dnf upgrade``

![](/Imagens/Instalation/Rust_download_linux.png)

Depois, para usar o instalador rustup, precisamos digitar o seguinte comando em nosso terminal. Execute: ``curl https://sh.rustup.rs -sSf | sh``

Agora, vamos configurar nosso shell atual usando o seguinte comando: ``source $HOME/.cargo/env``

Por fim, podemos verificar se a instação foi feita corretamente usando o comando: ``rustc --version``

OBS: ``sudo apt  install rustc``

## **Integração com Visual Studio Code**


## **Apoiadores**

- [Leonardo Saads Pinto](https://github.com/leonardoSaaads)