# Fastfetch customizado — Hardware / Software / Uptime

Layout do fastfetch com informações agrupadas em caixas (Hardware, Software e Uptime/Age/DT) e um módulo customizado de "idade do sistema".

## Pré-requisitos

- **fastfetch** instalado

## Passo a passo

### 1. Instale o fastfetch

**Fedora:**

```
sudo dnf install fastfetch
```

**Ubuntu**:

```
sudo apt update
sudo apt install fastfetch
```

**openSUSE:**

```
sudo zypper install fastfetch
```

**Arch Linux:**

```
sudo pacman -S fastfetch
```

  
Confirme a instalação:

```
fastfetch --version
```

### 2. Crie a pasta e o arquivo de configuração

Se a pasta ainda não existe, o jeito mais rápido é gerar a config padrão primeiro (isso já cria a pasta):

```
fastfetch --gen-config
```

Isso abre um gerador interativo. Basta apertar **S** ou **Enter** para salvar e sair — não precisa mudar nada nele, só usar pra criar o arquivo.

Alternativa manual (sem usar o gerador):

```
mkdir -p ~/.config/fastfetch
```

### 3. Substitua o conteúdo pelo config customizado

Abra o arquivo:

```
nano ~/.config/fastfetch/config.jsonc
```

Apague tudo e cole o conteúdo do `config.jsonc` deste projeto. Salve e feche.

### 4. Teste

```
fastfetch
```

## Estrutura do config

O arquivo é dividido em três blocos, cada um desenhado manualmente com módulos `"custom"` que imprimem as bordas Unicode (`┌─...─┐` / `└─...─┘`):

| Bloco | Cor | Módulos incluídos |
| - | - | - |
| **Hardware** | verde | host, cpu, gpu (via command truncado), memory, disk |
| **Software** | amarelo (OS/kernel/packages/shell) e azul (DE/display manager/WM/tema de ícones/terminal) | os, kernel, packages, shell, de, displaymanager, wm, icons, terminal |
| **Uptime / Age / DT** | magenta | OS Age (comando customizado), uptime, datetime |


### Detalhes importantes

- **Logo:** `"type": "auto"` usa automaticamente o logo ASCII da sua distro (no seu caso, Fedora).

- **OS Age:** não é um módulo nativo do fastfetch. É um comando shell que calcula a idade do sistema a partir da data de criação da partição raiz (`stat -c %W /`):

- ```
birth_install=$(stat -c %W /); current=$(date +%s); time_progression=$((current - birth_install)); days_difference=$((time_progression / 86400)); echo $days_difference days
```

- **GPU truncada:** como nomes de GPU integrada (ex: Xeon com gráfico integrado) podem ser muito longos e quebrar a linha, o módulo `gpu` foi trocado por um `command` que corta o texto em 42 caracteres:

- ```
fastfetch --logo none --pipe -s gpu | sed 's/^GPU: //' | cut -c1-42
```

- Para deixar mais curto ou mais longo, ajuste o número em `cut -c1-42`.

## Personalizações comuns

- **Cores:** troque `"keyColor"` de cada módulo (`green`, `yellow`, `blue`, `magenta`) por qualquer cor suportada pelo fastfetch, ou use `"keyColor": "38;2;R;G;B"` para uma cor RGB exata.

- **Cor das bordas:** o `\u001b[90m` no início de cada `"format"` das caixas é o código ANSI cinza. Troque por outro código para mudar a cor das bordas.

- **Adicionar/remover linhas:** qualquer módulo pode ser adicionado dentro do array `"modules"`. Lista completa de módulos disponíveis: rode `fastfetch --list-modules` ou consulte a [documentação oficial](https://github.com/fastfetch-cli/fastfetch/wiki/Configuration).

- **Trocar o logo:** troque `"type": "auto"` por `"type": "builtin"` com `"source": "nome-da-distro"` para forçar um logo específico, ou `"type": "auto"` com `"source": "/caminho/para/imagem.png"` para usar uma imagem própria.

## Rodar automaticamente ao abrir o terminal

Adicione ao final do seu shell de inicialização:

- **bash:** `~/.bashrc`

- **zsh:** `~/.zshrc`

- **fish:** `~/.config/fish/config.fish`

```
fastfetch
```

## Referências

- [Documentação oficial do fastfetch](https://github.com/fastfetch-cli/fastfetch)

- [Schema JSON de configuração](https://github.com/fastfetch-cli/fastfetch/raw/dev/doc/json_schema.json)

## Licença

[GPL-3.0 License](https://github.com/JoseJacsanCode/customFastFetch/blob/main/LICENSE)

