# Nixd video

## Install nixd, and set nixpkgs in nixpath
```nix
{ pkgs, inputs, ... }: 

{
  environment.systemPackages = [
    pkgs.nixd
  ];

  nix.nixPath = [ "nixpkgs=${inputs.nixpkgs}" ];

  # ...
}
```


## lspconfig nixd setup
```lua
require("lspconfig").nixd.setup({
  cmd = { "nixd" },
  settings = {
    nixd = {
      nixpkgs = {
        expr = "import <nixpkgs> { }",
      },
      formatting = {
        command = { "alejandra" }, -- or nixfmt or nixpkgs-fmt
      },
      -- options = {
      --   nixos = {
      --       expr = '(builtins.getFlake "/PATH/TO/FLAKE").nixosConfigurations.CONFIGNAME.options',
      --   },
      --   home_manager = {
      --       expr = '(builtins.getFlake "/PATH/TO/FLAKE").homeConfigurations.CONFIGNAME.options',
      --   },
      -- },
    },
  },
})
```


## vscode nix-ide setup

FILE PATH: /home/user/.config/Code/User/settings.json

```jsonc
{
  "nix.serverPath": "nixd",
  "nix.enableLanguageServer": true,
  "nix.serverSettings": {
    "nixd": {
      "formatting": {
        "command": [ "alejandra" ], // or nixfmt or nixpkgs-fmt
      },
      // "options": {
      //    "nixos": {
      //      "expr": "(builtins.getFlake \"/PATH/TO/FLAKE\").nixosConfigurations.CONFIGNAME.options"
      //    },
      //    "home_manager": {
      //      "expr": "(builtins.getFlake \"/PATH/TO/FLAKE\").homeConfigurations.CONFIGNAME.options"
      //    },
      // },
    }
  }
}
```

## emacs

```emacs-lisp
(use-package lsp-mode
  :ensure t)

;; For Without Tree Sitter
(use-package nix-mode
  :ensure t
  :hook
  (nix-mode . lsp-deferred)) ;; So that envrc mode will work

(use-package nix-mode
  :after lsp-mode
  :custom
  (lsp-disabled-clients '((nix-mode . nix-nil))) ;; Disable nil so that nixd will be used as lsp-server
  :config
  (setq lsp-nix-nixd-server-path "nixd"))

;; For Tree Sitter
(use-package nix-ts-mode
  :ensure t
  :mode "\\.nix\\'"
  :hook
  (nix-ts-mode . lsp-deferred)) ;; So that envrc mode will work

(use-package nix-ts-mode
  :after lsp-mode
  :custom
  (lsp-disabled-clients '((nix-ts-mode . nix-nil))) ;; Disable nil so that nixd will be used as lsp-server
  :config
  (setq lsp-nix-nixd-server-path "nixd"))
```

https://emacs-lsp.github.io/lsp-mode/page/lsp-nix/#installation
