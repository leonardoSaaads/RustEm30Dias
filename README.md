# **APRENDENDO RUST - LINGUAGEM DA INDUSTRIA 4.0**

Este repositório é destinado para o aprendizado da linguagem de programação Rust. Rust é uma linguagem de programação multiparadigma compilada desenvolvida pela Mozilla Research. É projetada para ser "segura, concorrente e prática", mas diferente de outras linguagens seguras, Rust não usa coletor de lixo. Possui suporte nativo ao WebAssembly e está se expandindo para diversas outras áreas ligadas à industria 4.0. Não fique de fora!

Sugerimos um Hands-On por dia durante 30 dias.

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
    <td>ESSENCIAL</td>
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
  <tr>
    <td>10</td>
    <td><a href="/HandsOn/HD10/">Structs - parte 01</a></td>
    <td>BÁSICO</td>
  </tr>
  <tr>
    <td>11</td>
    <td><a href="/HandsOn/HD11/">Structs - parte 02</a></td>
    <td>BÁSICO</td>
  </tr>
  <tr>
    <td>12</td>
    <td><a href="/HandsOn/HD10/">EXERCÍCIOS 02</a></td>
    <td>BÁSICO</td>
  </tr>
</tbody>
</table>


## **Status das atividades**

<label for="rust">❤️ Rust ❤️:</label>
<progress id="rust" value="99" max="100">99% </progress>
<br>

<label for="hdon">HANDS-ON:</label>
<progress id="hdon" value="23" max="100"> 23% </progress>
<br>

<label for="bugs">CORREÇÃO DE BUGS:</label>
<progress id="bugs" value="0" max="100"> 0% </progress>
<br>

<label for="discuss">NOVAS DISCUSSÕES:</label>
<progress id="discuss" value="0" max="100"> 0% </progress>
<br>

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

(Necessário fazer)

## **Apoiadores**

- [Leonardo Saads Pinto](https://github.com/leonardoSaaads)