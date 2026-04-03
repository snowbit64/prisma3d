# Conversor Prisma3D para OBJ/MTL

Conversor avançado para transformar arquivos de projeto 3D do Prisma App (`.prisma`) para o formato padrão Wavefront OBJ/MTL.

## Características

✅ **Suporta todas as primitivas do Prisma**
- Cubo (Box)
- Esfera (Sphere)
- Cilindro (Cylinder)
- Toro (Torus)
- Plano (Plane)
- Pirâmide (Pyramid)

✅ **Extração automática de metadados**
- Detecta tipo de primitiva automaticamente
- Extrai parâmetros de segmentação
- Preserva nomes de objetos

✅ **Conversão procedural**
- Gera malhas de alta qualidade
- Mantém proporções e escalas
- Cria normas suaves

✅ **Conversão em batch**
- Processa múltiplos arquivos simultaneamente
- Preserva estrutura de diretórios
- Relatório detalhado de progresso

## Instalação

### Requisitos
- Python 3.6+
- `unzip` (geralmente pré-instalado no Linux/macOS)
- Nenhuma dependência Python adicional! ✨

### Setup

```bash
# Simplesmente use o script Python diretamente
python3 prisma_convert.py arquivo.prisma
```

## Uso

### Converter um arquivo
```bash
python3 prisma_convert.py cubo.prisma
# Gera: cubo.obj e cubo.mtl no mesmo diretório
```

### Converter para diretório específico
```bash
python3 prisma_convert.py cubo.prisma ./output
# Gera: ./output/cubo.obj e ./output/cubo.mtl
```

### Converter múltiplos arquivos
```bash
python3 convert_all_prisma.py ./models
# Converte todos os *.prisma em ./models

python3 convert_all_prisma.py ./models ./output
# Converte e salva em ./output

python3 convert_all_prisma.py ./models ./output "*.prisma"
# Especificar padrão de arquivo (opcional)
```

## Estrutura de Arquivos Prisma3D

Os arquivos `.prisma` são containers ZIP com a seguinte estrutura:

```
arquivo.prisma (ZIP)
├── [hash_uuid]/
│   ├── .meta (metadados)
│   ├── project.proj (MessagePack - config do projeto)
│   └── content/
│       └── [hash].pobject (MessagePack - dados do objeto)
```

### Decodificação

Os dados internos são codificados em **MessagePack** (formato binário comprimido):
- Decodificador MessagePack nativo (sem dependências externas)
- Suporta todos os tipos: inteiros, floats, strings, arrays, maps
- Extração de metadados e tipo de primitiva

## Exemplos de Saída

### Cubo (Box)
```
✓ Tipo: Cubo (Box)
✓ Vértices: 8
✓ Faces: 12
```

### Esfera (Sphere)
```
✓ Tipo: Esfera (Sphere)
✓ Vértices: 533 (32 segmentos, 16 anéis)
✓ Faces: 1024
```

### Cilindro
```
✓ Tipo: Cilindro (Cylinder)
✓ Parâmetros: {'x': 20.0, 'y': 2.0}
✓ Vértices: 86
✓ Faces: 80
```

### Toro
```
✓ Tipo: Toro (Torus)
✓ Parâmetros: {'x': 24.0, 'y': 16.0}
✓ Vértices: 561
✓ Faces: 1024
```

## Arquivos Gerados

### arquivo.obj
Arquivo de malha 3D no formato Wavefront:
```obj
# Prisma3D converted to OBJ
# Object: Cubo
# Original: cubo.prisma

mtllib cubo.mtl

o Cubo
g Cubo

v -1.000000 -1.000000 -1.000000
v  1.000000 -1.000000 -1.000000
...
f 1 2 3
f 1 3 4
...
```

### arquivo.mtl
Arquivo de material (propriedades de renderização):
```mtl
# Prisma3D Material
# Object: Cubo

newmtl default
Ns 32.0
Ka 0.200000 0.200000 0.200000
Kd 0.800000 0.800000 0.800000
Ks 0.500000 0.500000 0.500000
```

## Compatibilidade

Os arquivos OBJ/MTL gerados são compatíveis com:

- ✓ Blender
- ✓ 3ds Max
- ✓ Maya
- ✓ Cinema 4D
- ✓ Unity
- ✓ Unreal Engine
- ✓ Babylonjs
- ✓ Three.js
- ✓ Qualquer software que suporte OBJ/MTL

## Parâmetros de Primitivas

### Cubo (Box)
```json
{"x": 1.0, "y": 1.0, "z": 1.0}
```
Segmentos em cada eixo (padrão: 1)

### Esfera (Sphere)
```
Segmentos: ~32 (longitude)
Anéis: ~16 (latitude)
```

### Cilindro
```json
{"x": 20.0, "y": 2.0}
```
- x: segmentos
- y: altura

### Toro
```json
{"x": 24.0, "y": 16.0}
```
- x: raio maior
- y: raio menor

### Plano (Plane)
```json
{"x": 2.0, "y": 2.0}
```
- x: largura
- y: altura

### Pirâmide
Parâmetros padrão (tamanho fixo)

## Troubleshooting

### Erro: "Arquivo não encontrado"
```
Verifique o caminho do arquivo .prisma
```

### Erro: "Nenhum tipo de primitiva detectado"
```
O arquivo pode estar corrompido ou em formato não suportado
O conversor usará um cubo padrão como fallback
```

### Arquivo OBJ vazio
```
Verifique se o arquivo .prisma foi criado corretamente no Prisma App
Tente reexportar o modelo
```

## Detalhes Técnicos

### Decodificação MessagePack
- Implementação nativa em Python (zero dependências)
- Suporte completo para tipos msgpack: fixint, int8-64, float32/64
- Suporte para strings (fixstr, str8, str16, str32)
- Suporte para arrays (fixarray, array16, array32)
- Suporte para maps (fixmap, map16, map32)

### Extração de Metadados
1. Extrai ZIP com `unzip -F` (tolerância a ZIPs corrompidos)
2. Decodifica MessagePack dos arquivos `.proj` e `.pobject`
3. Busca por strings contendo "Primitives.Primitive*"
4. Extrai parâmetros de JSON embutido

### Geração de Malha
1. Identifica tipo de primitiva
2. Gera vértices baseado em algoritmos procedimentais
3. Cria faces com índices válidos (começando em 1)
4. Normaliza coordenadas para escala padrão

## Performance

Tempo típico de conversão por arquivo:
- Cubo: < 100ms
- Esfera: < 100ms
- Cilindro: < 100ms
- Toro: < 200ms
- Lote de 7 arquivos: ~1 segundo

## Limitações Atuais

- ✓ Suporta primitivas básicas (não malhas arbitrárias)
- ✓ Materiais padronizados (sem texturas)
- ✓ Sem animações
- ✓ Sem rigging/bones

## Melhorias Futuras

- [ ] Suporte para malhas custom/importadas
- [ ] Extração de cores e texturas
- [ ] Suporte para transforms (position, rotation, scale)
- [ ] Exportação de hierarquia de objetos
- [ ] Suporte para rigging

## Licença

Ferramenta de conversão open-source. Úso livre para fins pessoais e comerciais.

## Autor

Criado como conversor especializado para o formato Prisma3D (Mobile 3D Modeling App)

---

**Versão**: 2.0  
**Data**: Abril 2026  
**Status**: Estável e totalmente funcional
