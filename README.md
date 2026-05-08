from kivy.core.window import Window
Window.softinput_mode = "below_target"

from kivy.app import App
from kivy.uix.screenmanager import ScreenManager, Screen, NoTransition
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.textinput import TextInput
from kivy.uix.button import Button
from kivy.uix.label import Label
from kivy.uix.scrollview import ScrollView
from kivy.uix.gridlayout import GridLayout
from kivy.uix.anchorlayout import AnchorLayout
from kivy.uix.floatlayout import FloatLayout
from kivy.uix.widget import Widget
from kivy.graphics import Color, Line, Rectangle, Ellipse, RoundedRectangle
from kivy.clock import Clock
from kivy.animation import Animation

import wikipedia
import re
import math
import json
import os
import random
import unicodedata
from datetime import datetime

wikipedia.set_lang("pt")
try:
    wikipedia.set_rate_limiting(True)
except Exception:
    pass


# =========================
# FRASES
# =========================
FRASES_MOTIVACIONAIS = [
    "Grandes coisas começam com pequenos passos.",
    "Todo especialista já foi iniciante um dia.",
    "Persistência vence talento quando talento não persiste.",
    "Cada linha de código é um passo para o futuro.",
    "Programar é transformar ideias em realidade.",
    "A mente que se expande nunca volta ao tamanho original.",
    "O impossível só demora um pouco mais.",
    "Disciplina constrói o que a motivação começa.",
    "Seu esforço de hoje constrói seu nível de amanhã.",
    "Toda criação incrível começou como uma ideia simples."
]

FRASES_SISTEMA = [
    "Carregando módulos cognitivos...",
    "Sincronizando núcleo inteligente...",
    "Compilando rotinas de resposta...",
    "Conectando camadas de conhecimento...",
    "Otimizando interface neural...",
    "Preparando sistema para interação...",
    "Analisando ambiente digital...",
    "Sistema pronto para interação."
]


# =========================
# UTILIDADES DE TEXTO
# =========================
def remove_acentos(text):
    return "".join(
        c for c in unicodedata.normalize("NFD", text)
        if unicodedata.category(c) != "Mn"
    )


def normalize(text):
    text = remove_acentos(text.lower().strip())
    text = re.sub(r"\s+", " ", text)
    return text


def clean(text):
    return normalize(text).strip("?!.,;:")


# =========================
# BASE ESCOLAR PADRÃO
# =========================
BASE_CONHECIMENTO_PADRAO = {
    "matematica": {
        "fundamental_1": {
            "adicao": {
                "definicao": "Adição é a operação matemática usada para somar quantidades.",
                "exemplo": "2 + 3 = 5.",
                "exercicio": "Quanto é 7 + 8?",
                "resposta": "15"
            },
            "subtracao": {
                "definicao": "Subtração é a operação de retirar uma quantidade de outra.",
                "exemplo": "9 - 4 = 5.",
                "exercicio": "Quanto é 15 - 6?",
                "resposta": "9"
            },
            "multiplicacao": {
                "definicao": "Multiplicação é uma soma repetida de um número.",
                "exemplo": "3 x 4 = 12.",
                "exercicio": "Quanto é 6 x 5?",
                "resposta": "30"
            },
            "divisao": {
                "definicao": "Divisão é repartir um número em partes iguais.",
                "exemplo": "12 ÷ 3 = 4.",
                "exercicio": "Quanto é 20 ÷ 5?",
                "resposta": "4"
            }
        },
        "fundamental_2": {
            "fracao": {
                "definicao": "Fração representa partes de um todo.",
                "exemplo": "Se uma pizza foi dividida em 8 partes e você comeu 4, então comeu 4/8 da pizza.",
                "exercicio": "Quanto é 1/2 de 20?",
                "resposta": "10"
            },
            "porcentagem": {
                "definicao": "Porcentagem representa uma razão baseada em 100.",
                "exemplo": "50% significa metade.",
                "exercicio": "Quanto é 25% de 200?",
                "resposta": "50"
            },
            "potenciacao": {
                "definicao": "Potenciação é a multiplicação repetida de um número.",
                "exemplo": "2³ = 8.",
                "exercicio": "Quanto é 4²?",
                "resposta": "16"
            },
            "raiz quadrada": {
                "definicao": "Raiz quadrada é o número que multiplicado por ele mesmo gera outro número.",
                "exemplo": "√9 = 3.",
                "exercicio": "Qual é √16?",
                "resposta": "4"
            },
            "regra de tres": {
                "definicao": "Regra de três é usada para resolver proporções.",
                "exemplo": "Se 2 maçãs custam 4 reais, 4 maçãs custam 8 reais.",
                "exercicio": "Se 3 cadernos custam 9 reais, quanto custam 6?",
                "resposta": "18"
            }
        },
        "ensino_medio": {
            "equacao do primeiro grau": {
                "definicao": "Equação do primeiro grau possui incógnita elevada à potência 1.",
                "exemplo": "x + 4 = 10.",
                "exercicio": "Resolva: x + 6 = 11.",
                "resposta": "x = 5"
            },
            "equacao do segundo grau": {
                "definicao": "Equação do segundo grau possui incógnita elevada ao quadrado.",
                "exemplo": "x² - 3x + 2 = 0.",
                "exercicio": "Resolva: x² - 5x + 6 = 0.",
                "resposta": "x = 2 ou x = 3"
            },
            "bhaskara": {
                "definicao": "A fórmula de Bhaskara resolve equações do segundo grau.",
                "exemplo": "x = (-b ± √(b² - 4ac)) / 2a.",
                "exercicio": "Resolva: x² - 5x + 6 = 0.",
                "resposta": "x = 2 ou x = 3"
            },
            "funcao": {
                "definicao": "Função é uma relação onde cada entrada tem apenas uma saída.",
                "exemplo": "f(x) = 2x.",
                "exercicio": "Se f(x)=3x, qual f(5)?",
                "resposta": "15"
            },
            "funcao afim": {
                "definicao": "Função afim tem formato f(x)=ax+b.",
                "exemplo": "f(x)=2x+1.",
                "exercicio": "Se f(x)=2x+1, qual f(3)?",
                "resposta": "7"
            },
            "funcao quadratica": {
                "definicao": "Função quadrática possui forma f(x)=ax²+bx+c.",
                "exemplo": "f(x)=x².",
                "exercicio": "Se f(x)=x², qual f(4)?",
                "resposta": "16"
            },
            "logaritmo": {
                "definicao": "Logaritmo indica o expoente ao qual devemos elevar uma base.",
                "exemplo": "log₁₀(100)=2.",
                "exercicio": "log₂(8)=?",
                "resposta": "3"
            },
            "trigonometria": {
                "definicao": "Trigonometria estuda relações entre ângulos e lados de triângulos.",
                "exemplo": "sen(30°)=0,5.",
                "exercicio": "Qual o valor de sen(90°)?",
                "resposta": "1"
            },
            "progressao aritmetica": {
                "definicao": "Progressão aritmética é uma sequência com diferença constante.",
                "exemplo": "2, 4, 6, 8...",
                "exercicio": "Qual o próximo número: 3, 6, 9, ...?",
                "resposta": "12"
            },
            "progressao geometrica": {
                "definicao": "Progressão geométrica é uma sequência multiplicativa.",
                "exemplo": "2, 4, 8, 16...",
                "exercicio": "Qual o próximo número: 3, 9, 27, ...?",
                "resposta": "81"
            }
        },
        "superior": {
            "limite": {
                "definicao": "Limite é o valor que uma função se aproxima quando a variável se aproxima de um ponto.",
                "exemplo": "lim(x→0) sen(x)/x = 1.",
                "exercicio": "Qual o limite de x² quando x→0?",
                "resposta": "0"
            },
            "derivada": {
                "definicao": "Derivada mede a taxa de variação de uma função.",
                "exemplo": "Se f(x)=x², então f'(x)=2x.",
                "exercicio": "Qual a derivada de x³?",
                "resposta": "3x²"
            },
            "integral": {
                "definicao": "Integral calcula áreas sob curvas e soma acumulada.",
                "exemplo": "∫x dx = x²/2 + C.",
                "exercicio": "Qual a integral de 2x?",
                "resposta": "x² + C"
            },
            "algebra linear": {
                "definicao": "Álgebra linear estuda vetores, matrizes e sistemas lineares.",
                "exemplo": "Uma matriz 2x2 possui 2 linhas e 2 colunas.",
                "exercicio": "Quantas linhas tem uma matriz 3x2?",
                "resposta": "3"
            },
            "probabilidade": {
                "definicao": "Probabilidade mede a chance de um evento ocorrer.",
                "exemplo": "A chance de sair cara em uma moeda é 1/2.",
                "exercicio": "Qual a probabilidade de sair 6 em um dado?",
                "resposta": "1/6"
            },
            "estatistica": {
                "definicao": "Estatística analisa dados, distribuições e medidas como média, mediana e moda.",
                "exemplo": "A média de 2, 4 e 6 é 4.",
                "exercicio": "Qual a média de 2, 4 e 6?",
                "resposta": "4"
            }
        }
    },
    "portugues": {
        "fundamental_2": {
            "substantivo": {
                "definicao": "Substantivo é a palavra que nomeia seres, lugares, objetos, sentimentos e ideias.",
                "exemplo": "Casa, amor, Brasil e alegria são substantivos.",
                "exercicio": "Na frase 'João comprou um caderno', qual é um substantivo?",
                "resposta": "João e caderno."
            },
            "verbo": {
                "definicao": "Verbo é a palavra que indica ação, estado ou fenômeno da natureza.",
                "exemplo": "Correr, estudar, ser e chover são verbos.",
                "exercicio": "Na frase 'Maria estudou ontem', qual é o verbo?",
                "resposta": "Estudou."
            },
            "adjetivo": {
                "definicao": "Adjetivo é a palavra que caracteriza ou qualifica um substantivo.",
                "exemplo": "Na frase 'casa bonita', bonita é adjetivo.",
                "exercicio": "Na frase 'menino inteligente', qual é o adjetivo?",
                "resposta": "Inteligente."
            }
        }
    },
    "historia": {
        "fundamental_2": {
            "revolucao francesa": {
                "definicao": "A Revolução Francesa foi um movimento iniciado em 1789 que transformou a França e influenciou o mundo.",
                "exemplo": "Ela defendia liberdade, igualdade e fraternidade.",
                "exercicio": "Em que ano começou a Revolução Francesa?",
                "resposta": "1789"
            },
            "idade media": {
                "definicao": "A Idade Média foi um período histórico entre a queda do Império Romano do Ocidente e o início da Idade Moderna.",
                "exemplo": "Foi marcada pelo feudalismo e pela forte influência da Igreja.",
                "exercicio": "A Idade Média veio antes ou depois da Idade Moderna?",
                "resposta": "Antes."
            }
        }
    },
    "geografia": {
        "fundamental_2": {
            "clima": {
                "definicao": "Clima é o conjunto de condições atmosféricas observadas por longos períodos em uma região.",
                "exemplo": "O clima equatorial é quente e úmido.",
                "exercicio": "O clima é observado em curto ou longo prazo?",
                "resposta": "Longo prazo."
            },
            "relevo": {
                "definicao": "Relevo é o conjunto das formas da superfície terrestre.",
                "exemplo": "Montanhas, planaltos, planícies e depressões são formas de relevo.",
                "exercicio": "Montanhas fazem parte do relevo?",
                "resposta": "Sim."
            }
        }
    },
    "biologia": {
        "fundamental_2": {
            "fotossintese": {
                "definicao": "Fotossíntese é o processo em que plantas produzem seu alimento usando luz, água e gás carbônico.",
                "exemplo": "A planta usa a energia solar para formar glicose.",
                "exercicio": "Qual gás a planta absorve na fotossíntese?",
                "resposta": "Gás carbônico."
            },
            "celula": {
                "definicao": "Célula é a menor unidade estrutural e funcional dos seres vivos.",
                "exemplo": "O corpo humano é formado por bilhões de células.",
                "exercicio": "A célula é considerada a menor unidade do quê?",
                "resposta": "Dos seres vivos."
            }
        }
    },
    "fisica": {
        "ensino_medio": {
            "velocidade": {
                "definicao": "Velocidade é a relação entre a distância percorrida e o tempo gasto.",
                "exemplo": "Se um carro percorre 100 km em 2 horas, sua velocidade média é 50 km/h.",
                "exercicio": "Se você percorre 60 km em 2 horas, qual é a velocidade média?",
                "resposta": "30 km/h"
            }
        }
    },
    "quimica": {
        "ensino_medio": {
            "atomo": {
                "definicao": "Átomo é a menor parte de um elemento químico que ainda mantém suas propriedades.",
                "exemplo": "Todo átomo possui núcleo e eletrosfera.",
                "exercicio": "O átomo é a menor parte de quê?",
                "resposta": "De um elemento químico."
            }
        }
    }
}


# =========================
# MEMÓRIA
# =========================
ARQUIVO_MEMORIA = "memoria.json"


def carregar_memoria():
    if os.path.exists(ARQUIVO_MEMORIA):
        try:
            with open(ARQUIVO_MEMORIA, "r", encoding="utf-8") as f:
                return json.load(f)
        except Exception:
            return {}
    return {}


def salvar_memoria(mem):
    try:
        with open(ARQUIVO_MEMORIA, "w", encoding="utf-8") as f:
            json.dump(mem, f, ensure_ascii=False, indent=4)
    except Exception as e:
        print("Erro ao salvar memória:", e)


memoria = carregar_memoria()


# =========================
# PROGRESSO
# =========================
ARQUIVO_PROGRESSO = "progresso.json"


def carregar_progresso():
    if os.path.exists(ARQUIVO_PROGRESSO):
        try:
            with open(ARQUIVO_PROGRESSO, "r", encoding="utf-8") as f:
                return json.load(f)
        except Exception:
            return {"xp": 0, "nivel": 1}
    return {"xp": 0, "nivel": 1}


def salvar_progresso(p):
    try:
        with open(ARQUIVO_PROGRESSO, "w", encoding="utf-8") as f:
            json.dump(p, f, ensure_ascii=False, indent=4)
    except Exception as e:
        print("Erro ao salvar progresso:", e)


progresso = carregar_progresso()


def xp_limite_por_nivel(nivel):
    if nivel == 1:
        return 50
    if nivel == 2:
        return 150
    if nivel == 3:
        return 300
    return 500


def xp_inicio_nivel(nivel):
    if nivel == 1:
        return 0
    if nivel == 2:
        return 50
    if nivel == 3:
        return 150
    if nivel == 4:
        return 300
    return 500


def ganhar_xp(qtd):
    progresso["xp"] += qtd

    xp = progresso["xp"]
    novo_nivel = 1

    if xp >= 300:
        novo_nivel = 4
    elif xp >= 150:
        novo_nivel = 3
    elif xp >= 50:
        novo_nivel = 2

    subiu = False
    if novo_nivel > progresso["nivel"]:
        progresso["nivel"] = novo_nivel
        subiu = True

    salvar_progresso(progresso)
    return subiu


# =========================
# CONHECIMENTOS CURTOS
# =========================
ARQUIVO_CONHECIMENTOS = "conhecimentos.json"


def carregar_conhecimentos():
    if os.path.exists(ARQUIVO_CONHECIMENTOS):
        try:
            with open(ARQUIVO_CONHECIMENTOS, "r", encoding="utf-8") as f:
                return json.load(f)
        except Exception:
            return {}
    return {}


conhecimentos = carregar_conhecimentos()


# =========================
# BASE DE CONHECIMENTO ESCOLAR
# =========================
ARQUIVO_BASE = "base_conhecimento.json"


def carregar_base_conhecimento():
    if os.path.exists(ARQUIVO_BASE):
        try:
            with open(ARQUIVO_BASE, "r", encoding="utf-8") as f:
                base = json.load(f)
                if isinstance(base, dict) and base:
                    print("Base escolar carregada do arquivo JSON.")
                    return base
        except Exception as e:
            print("Erro ao carregar base de conhecimento do arquivo:", e)

    print("Usando base escolar padrão embutida no script.")
    return json.loads(json.dumps(BASE_CONHECIMENTO_PADRAO))


base_conhecimento = carregar_base_conhecimento()
print("BASE CARREGADA:", list(base_conhecimento.keys()))


# =========================
# CONTEXTO
# =========================
contexto = {
    "ultimo_tema": None,
    "ultimo_estado": None,
    "aguardando_detalhe": False
}


# =========================
# IA
# =========================
FAQ = {
    "quem descobriu o brasil": "Pedro Álvares Cabral, em 1500.",
    "qual a capital do brasil": "Brasília.",
    "qual seu nome": "Eu sou a HenricoAI.",
    "o que e python": "Python é uma linguagem de programação muito usada para automação e inteligência artificial.",
    "o que e algoritmo": "Algoritmo é uma sequência de passos para resolver um problema.",
    "o que e variavel": "Variável é um espaço que guarda um valor.",
    "o que e loop": "Loop é uma repetição de código várias vezes.",
    "o que e um loop": "Loop é uma repetição de código várias vezes."
}


def is_creator_question(text):
    gatilhos = [
        "quem e seu criador",
        "quem te criou",
        "quem criou voce",
        "qual seu criador",
        "qual e seu criador"
    ]
    t = clean(text)
    return any(g in t for g in gatilhos) or "criador" in t


def math_answer(text):
    expr = clean(text).replace("x", "*")
    if not re.fullmatch(r"[0-9\+\-\*\/\.\(\)\s]+", expr):
        return None
    try:
        result = eval(expr, {"__builtins__": {}}, {})
        if isinstance(result, float) and result.is_integer():
            result = int(result)
        return str(result)
    except Exception:
        return None


def pesquisar(pergunta):
    try:
        return wikipedia.summary(pergunta, sentences=2)
    except wikipedia.exceptions.DisambiguationError:
        return "Essa pergunta tem vários significados. Seja mais específico."
    except wikipedia.exceptions.PageError:
        return None
    except Exception as e:
        print("Erro Wikipedia:", e)
        return None


def responder_conhecimento(text):
    return conhecimentos.get(clean(text))


def aprender(text):
    t = clean(text)

    if "meu nome e " in t:
        nome = t.replace("meu nome e ", "").strip()
        if nome:
            memoria["nome"] = nome.title()
            salvar_memoria(memoria)
            return f"Prazer, {memoria['nome']}. Vou lembrar do seu nome."

    if "eu gosto de " in t:
        gosto = t.replace("eu gosto de ", "").strip()
        if gosto:
            memoria["gosto"] = gosto
            salvar_memoria(memoria)
            return f"Entendi. Vou lembrar que você gosta de {gosto}."

    if "minha cor favorita e " in t:
        cor = t.replace("minha cor favorita e ", "").strip()
        if cor:
            memoria["cor_favorita"] = cor
            salvar_memoria(memoria)
            return f"Beleza. Vou lembrar que sua cor favorita é {cor}."

    return None


def responder_com_memoria(text):
    t = clean(text)

    if "qual meu nome" in t:
        return f"Seu nome é {memoria['nome']}." if "nome" in memoria else "Você ainda não me disse seu nome."

    if "do que eu gosto" in t or "o que eu gosto" in t:
        return f"Você gosta de {memoria['gosto']}." if "gosto" in memoria else "Você ainda não me contou do que gosta."

    if "qual minha cor favorita" in t:
        return f"Sua cor favorita é {memoria['cor_favorita']}." if "cor_favorita" in memoria else "Você ainda não me contou sua cor favorita."

    return None


def responder_conversa(text):
    t = clean(text)

    if t in ["oi", "ola", "eai", "e ai", "bom dia", "boa tarde", "boa noite"]:
        contexto["ultimo_tema"] = "saudacao"
        contexto["aguardando_detalhe"] = False
        return "Olá. É bom falar com você. Como posso te ajudar hoje?"

    if t in ["obrigado", "obrigada", "valeu", "muito obrigado", "muito obrigada"]:
        return "De nada. Fico feliz em poder ajudar."

    if t in ["tudo bem", "como voce esta"]:
        return "Estou bem e pronto para conversar com você. E você, como está se sentindo?"

    if "estou triste" in t or "to triste" in t:
        contexto["ultimo_tema"] = "desabafo"
        contexto["ultimo_estado"] = "tristeza"
        contexto["aguardando_detalhe"] = True
        return "Sinto muito que você esteja se sentindo assim. Se quiser, pode me contar o que aconteceu. Eu vou te ouvir com calma."

    if "estou mal" in t or "to mal" in t or "nao estou bem" in t:
        contexto["ultimo_tema"] = "desabafo"
        contexto["ultimo_estado"] = "mal"
        contexto["aguardando_detalhe"] = True
        return "Poxa, sinto muito por isso. Se quiser desabafar, pode falar comigo. Às vezes colocar pra fora já ajuda um pouco."

    if "estou cansado" in t or "to cansado" in t or "estou cansada" in t or "to cansada" in t:
        contexto["ultimo_tema"] = "desabafo"
        contexto["ultimo_estado"] = "cansaco"
        contexto["aguardando_detalhe"] = True
        return "Entendo. Às vezes o cansaço pesa mesmo. Quer conversar um pouco ou tentar organizar o que está te sobrecarregando?"

    if "posso falar com voce" in t or "posso conversar com voce" in t:
        contexto["ultimo_tema"] = "conversa"
        contexto["aguardando_detalhe"] = True
        return "Claro. Pode falar comigo. Estou aqui para te ouvir."

    if "voce e legal" in t or "gostei de voce" in t:
        return "Fico feliz em ouvir isso. Obrigado por dizer isso."

    return None


def responder_contexto(text):
    t = clean(text)

    if contexto["ultimo_tema"] == "desabafo":
        if t in ["sim", "e", "foi", "aham"]:
            return "Pode me contar com calma. O que aconteceu exatamente?"

        if t in ["nao"]:
            contexto["aguardando_detalhe"] = False
            return "Tudo bem. Se mudar de ideia, eu continuo aqui para conversar com você."

        if "foi por causa" in t:
            contexto["aguardando_detalhe"] = True
            return "Entendi. Isso realmente pode mexer muito com a gente. Quer me contar um pouco mais sobre essa situação?"

        if "isso me machucou" in t or "me machucou" in t:
            contexto["aguardando_detalhe"] = True
            return "Imagino como isso deve ter doído. Quer me explicar melhor o que te machucou nisso?"

        if "nao sei" in t:
            return "Tudo bem não saber explicar de imediato. Pode começar do jeito que conseguir. Eu vou te acompanhando."

        if contexto["aguardando_detalhe"] and len(t.split()) >= 4:
            contexto["aguardando_detalhe"] = False
            return "Entendi. Obrigado por me contar isso. Parece que isso te afetou de verdade. Quer que eu te ouça mais ou prefere tentar pensar comigo no que fazer agora?"

    if contexto["ultimo_tema"] == "conversa":
        if t in ["sim", "claro", "quero"]:
            return "Pode falar. Estou te ouvindo."
        if t in ["nao"]:
            return "Tudo bem. Quando quiser conversar, eu estarei aqui."

    return None


# =========================
# BASE DE CONHECIMENTO ESCOLAR
# =========================
EXPRESSOES_REMOVER_BASE = [
    "o que e",
    "oque e",
    "explique",
    "me explique",
    "ensine",
    "me ensine",
    "fale sobre",
    "me fale sobre",
    "quero saber",
    "me diga",
    "sobre",
    "o que seria",
    "o que significa",
    "defina",
    "quero exercicio de",
    "quero exercicios de",
    "me passa exercicio de",
    "me passe exercicio de",
    "me passa exercicios de",
    "me passe exercicios de",
    "me de exercicio de",
    "me de exercicios de",
    "resposta do exercicio de",
    "resposta do exercicio",
    "resposta de exercicio de",
    "qual a resposta do exercicio de",
    "qual a resposta do exercicio"
]


def simplificar_pergunta_base(pergunta):
    pergunta_limpa = clean(pergunta)

    for exp in EXPRESSOES_REMOVER_BASE:
        pergunta_limpa = pergunta_limpa.replace(exp, " ")

    pergunta_limpa = re.sub(r"\s+", " ", pergunta_limpa).strip()
    return pergunta_limpa


def buscar_na_base(pergunta):
    if not base_conhecimento:
        return None

    pergunta_original = clean(pergunta)
    pergunta_simplificada = simplificar_pergunta_base(pergunta)

    for materia, niveis in base_conhecimento.items():
        for nivel, assuntos in niveis.items():
            for assunto, dados in assuntos.items():
                assunto_limpo = clean(assunto.replace("_", " "))

                if (
                    assunto_limpo == pergunta_simplificada
                    or assunto_limpo in pergunta_original
                    or assunto_limpo in pergunta_simplificada
                    or (pergunta_simplificada and pergunta_simplificada in assunto_limpo)
                ):
                    return {
                        "materia": materia,
                        "nivel": nivel,
                        "assunto": assunto,
                        "dados": dados
                    }

    return None


def responder_base_conhecimento(pergunta):
    resultado = buscar_na_base(pergunta)

    if not resultado:
        return None

    dados = resultado["dados"]
    assunto = resultado["assunto"].replace("_", " ").title()
    materia = resultado["materia"].replace("_", " ").title()
    nivel = resultado["nivel"].replace("_", " ").title()
    pergunta_limpa = clean(pergunta)

    quer_exercicio = "exercicio" in pergunta_limpa or "exercicios" in pergunta_limpa
    quer_resposta = "resposta" in pergunta_limpa
    quer_explicacao = (
        "o que e" in pergunta_limpa
        or "explique" in pergunta_limpa
        or "ensine" in pergunta_limpa
        or "fale sobre" in pergunta_limpa
    )

    if quer_resposta and quer_exercicio:
        return f"Resposta do exercício de {assunto}: {dados.get('resposta', 'Não encontrei a resposta.')}"

    if quer_exercicio and not quer_resposta:
        return f"Exercício de {assunto}: {dados.get('exercicio', 'Não encontrei exercício.')}"

    if quer_explicacao:
        partes = [
            f"{assunto}",
            f"Matéria: {materia}",
            f"Nível: {nivel}"
        ]

        if "definicao" in dados:
            partes.append(f"Definição: {dados['definicao']}")

        if "exemplo" in dados:
            partes.append(f"Exemplo: {dados['exemplo']}")

        if "exercicio" in dados:
            partes.append(f"Exercício: {dados['exercicio']}")

        return "\n\n".join(partes)

    return f"{assunto}: {dados.get('definicao', 'Não encontrei definição.')}"


def responder(text):
    pergunta = clean(text)

    aprendizado = aprender(text)
    if aprendizado:
        return aprendizado

    memoria_resp = responder_com_memoria(text)
    if memoria_resp:
        return memoria_resp

    contexto_resp = responder_contexto(text)
    if contexto_resp:
        return contexto_resp

    conversa_resp = responder_conversa(text)
    if conversa_resp:
        return conversa_resp

    if is_creator_question(text):
        return "Meu criador é o Henrico."

    if pergunta in FAQ:
        return FAQ[pergunta]

    conhecimento_resp = responder_conhecimento(text)
    if conhecimento_resp:
        return conhecimento_resp

    base_resp = responder_base_conhecimento(text)
    if base_resp:
        return base_resp

    conta = math_answer(text)
    if conta:
        return conta

    pesquisa = pesquisar(text)
    if pesquisa:
        return pesquisa

    return "Não tenho certeza sobre isso ainda, mas quero tentar te ajudar. Se puder, me explique de outro jeito ou me dê mais detalhes."


# =========================
# VISUAL
# =========================
class CapsuleBar(BoxLayout):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.flash_power = 0

        with self.canvas.before:
            self.glow_color = Color(0.08, 0.55, 1.0, 0.0)
            self.glow = RoundedRectangle(radius=[28])
            Color(0.12, 0.12, 0.14, 1)
            self.bg = RoundedRectangle(radius=[28])

        self.bind(pos=self._update_bg, size=self._update_bg)
        Clock.schedule_interval(self._animate_glow, 1 / 60)

    def flash(self):
        self.flash_power = 1.0

    def _animate_glow(self, dt):
        if self.flash_power > 0:
            self.flash_power = max(0, self.flash_power - dt * 2.2)

        pad = 16 + self.flash_power * 24
        self.glow.pos = (self.x - pad / 2, self.y - pad / 2)
        self.glow.size = (self.width + pad, self.height + pad)
        self.glow_color.a = 0.30 * self.flash_power

    def _update_bg(self, *args):
        self.bg.pos = self.pos
        self.bg.size = self.size
        self.glow.pos = self.pos
        self.glow.size = self.size


class AnimatedCore(Widget):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.t = 0
        self.active = False
        self.flash_power = 0

        with self.canvas:
            Color(0.0, 0.45, 1.0, 0.10)
            self.outer_glow = Ellipse(size=(60, 60), pos=(0, 0))
            Color(0.0, 0.75, 1.0, 0.18)
            self.mid_glow = Ellipse(size=(46, 46), pos=(0, 0))
            Color(0.0, 0.95, 1.0, 0.90)
            self.ring_outer = Line(circle=(0, 0, 18, 0, 300), width=1.8)
            Color(0.0, 0.75, 1.0, 0.85)
            self.ring_outer_gap = Line(circle=(0, 0, 18, 320, 360), width=1.8)
            Color(0.0, 1.0, 1.0, 0.95)
            self.ring_inner = Line(circle=(0, 0, 11, 40, 280), width=1.3)
            Color(0.0, 0.85, 1.0, 0.85)
            self.ring_inner_gap = Line(circle=(0, 0, 11, 300, 360), width=1.3)
            Color(0.0, 1.0, 1.0, 0.25)
            self.core_fill = Ellipse(size=(14, 14), pos=(0, 0))
            Color(0.0, 1.0, 1.0, 0.95)
            self.dot = Ellipse(size=(6, 6), pos=(0, 0))
            Color(0.0, 1.0, 1.0, 0.85)
            self.orbit_dot_1 = Ellipse(size=(4, 4), pos=(0, 0))
            Color(0.0, 0.75, 1.0, 0.85)
            self.orbit_dot_2 = Ellipse(size=(4, 4), pos=(0, 0))

        self.bind(pos=self._update_draw, size=self._update_draw)
        Clock.schedule_interval(self.animate, 1 / 60)

    def set_active(self, value):
        self.active = value
        if value:
            self.flash_power = 1.0

    def flash(self):
        self.flash_power = 1.2

    def animate(self, dt):
        speed = 6.0 if self.active else 2.4
        self.t += dt * speed
        if self.flash_power > 0:
            self.flash_power = max(0, self.flash_power - dt * 2.2)
        self._update_draw()

    def _update_draw(self, *args):
        cx = self.center_x
        cy = self.center_y

        pulse = math.sin(self.t * 2.0) * (2.0 if self.active else 1.0)
        glow_bonus = self.flash_power * 12

        outer = 60 + pulse * 2 + glow_bonus
        mid = 46 + pulse * 1.4 + glow_bonus * 0.7
        ring1 = 18 + pulse * 0.5
        ring2 = 11 + pulse * 0.35
        core = 14 + pulse * 0.4 + self.flash_power * 2
        dot = 6 + self.flash_power * 1.2

        self.outer_glow.size = (outer, outer)
        self.outer_glow.pos = (cx - outer / 2, cy - outer / 2)
        self.mid_glow.size = (mid, mid)
        self.mid_glow.pos = (cx - mid / 2, cy - mid / 2)

        ang1 = (self.t * 70) % 360
        self.ring_outer.circle = (cx, cy, ring1, ang1, (ang1 + 260) % 360)
        self.ring_outer_gap.circle = (cx, cy, ring1, (ang1 + 285) % 360, (ang1 + 360) % 360)

        ang2 = (-self.t * 110) % 360
        self.ring_inner.circle = (cx, cy, ring2, ang2, (ang2 + 220) % 360)
        self.ring_inner_gap.circle = (cx, cy, ring2, (ang2 + 250) % 360, (ang2 + 360) % 360)

        self.core_fill.size = (core, core)
        self.core_fill.pos = (cx - core / 2, cy - core / 2)
        self.dot.size = (dot, dot)
        self.dot.pos = (cx - dot / 2, cy - dot / 2)

        a1 = self.t * 2.0
        a2 = -self.t * 2.8
        r_orbit_1 = ring1 + 3
        r_orbit_2 = ring2 + 5

        self.orbit_dot_1.pos = (cx + math.cos(a1) * r_orbit_1 - 2, cy + math.sin(a1) * r_orbit_1 - 2)
        self.orbit_dot_2.pos = (cx + math.cos(a2) * r_orbit_2 - 2, cy + math.sin(a2) * r_orbit_2 - 2)


class ParticleNetwork(Widget):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.points = []
        self.num_points = 46
        self.pulse_power = 0.0

        Clock.schedule_interval(self.update_network, 1 / 60)

        for _ in range(self.num_points):
            self.points.append({
                "x": random.uniform(0, Window.width),
                "y": random.uniform(0, Window.height),
                "vx": random.uniform(-10, 10),
                "vy": random.uniform(-10, 10),
                "size": random.uniform(2.2, 3.8),
                "pulse": random.uniform(0, math.pi * 2)
            })

    def trigger_pulse(self):
        self.pulse_power = 1.0

    def update_network(self, dt):
        width = self.width if self.width > 0 else Window.width
        height = self.height if self.height > 0 else Window.height

        if self.pulse_power > 0:
            self.pulse_power = max(0, self.pulse_power - dt * 1.8)

        self.canvas.clear()

        for p in self.points:
            p["x"] += p["vx"] * dt
            p["y"] += p["vy"] * dt
            p["pulse"] += dt * (2.0 + self.pulse_power * 3.0)

            if p["x"] <= 0 or p["x"] >= width:
                p["vx"] *= -1
            if p["y"] <= 0 or p["y"] >= height:
                p["vy"] *= -1

        max_dist = 260 + self.pulse_power * 35

        with self.canvas:
            if self.pulse_power > 0:
                cx = width / 2
                cy = height / 2
                halo = 180 + self.pulse_power * 260
                Color(0.0, 0.9, 1.0, 0.06 * self.pulse_power)
                Ellipse(pos=(cx - halo / 2, cy - halo / 2), size=(halo, halo))

            for i in range(len(self.points)):
                for j in range(i + 1, len(self.points)):
                    p1 = self.points[i]
                    p2 = self.points[j]
                    dx = p1["x"] - p2["x"]
                    dy = p1["y"] - p2["y"]
                    dist = math.sqrt(dx * dx + dy * dy)

                    if dist < max_dist:
                        alpha_base = 0.30 * (1 - dist / max_dist)
                        alpha = max(0.08, alpha_base + self.pulse_power * 0.20)

                        Color(0.0, 0.85, 1.0, alpha * 0.40)
                        Line(points=[p1["x"], p1["y"], p2["x"], p2["y"]], width=2.4 + self.pulse_power * 1.4)

                        Color(0.0, 1.0, 1.0, min(0.95, alpha))
                        Line(points=[p1["x"], p1["y"], p2["x"], p2["y"]], width=1.05 + self.pulse_power * 0.35)

            for p in self.points:
                pulse = (math.sin(p["pulse"]) + 1) / 2
                extra = self.pulse_power * 4.5

                glow_size = p["size"] + 5 + pulse * 3 + extra
                mid_size = p["size"] + 2 + pulse * 1.5 + extra * 0.5
                core_size = p["size"] + pulse * 0.8 + self.pulse_power * 1.0

                Color(0.0, 0.75, 1.0, 0.14 + pulse * 0.10 + self.pulse_power * 0.10)
                Ellipse(pos=(p["x"] - glow_size / 2, p["y"] - glow_size / 2), size=(glow_size, glow_size))

                Color(0.0, 0.95, 1.0, 0.24 + pulse * 0.12 + self.pulse_power * 0.14)
                Ellipse(pos=(p["x"] - mid_size / 2, p["y"] - mid_size / 2), size=(mid_size, mid_size))

                Color(0.0, 1.0, 1.0, min(1, 0.82 + self.pulse_power * 0.18))
                Ellipse(pos=(p["x"] - core_size / 2, p["y"] - core_size / 2), size=(core_size, core_size))


class BubbleBox(BoxLayout):
    def __init__(self, text="", is_user=False, time_text="", **kwargs):
        super().__init__(orientation="vertical", size_hint_y=None, padding=(18, 14), spacing=6, **kwargs)

        self.label = Label(text=text, halign="left", valign="middle", font_size=18, color=(1, 1, 1, 1), size_hint_y=None)
        self.time_label = Label(text=time_text, halign="right", valign="middle", font_size=11, color=(0.82, 0.82, 0.82, 0.85), size_hint_y=None)

        self.label.bind(width=lambda *x: setattr(self.label, "text_size", (self.label.width, None)))
        self.time_label.bind(width=lambda *x: setattr(self.time_label, "text_size", (self.time_label.width, None)))
        self.label.bind(texture_size=self._update_height)
        self.time_label.bind(texture_size=self._update_height)

        self.add_widget(self.label)
        self.add_widget(self.time_label)

        with self.canvas.before:
            Color(*(0.19, 0.19, 0.23, 1) if is_user else (0.06, 0.56, 0.95, 1))
            self.bg = RoundedRectangle(radius=[24])

        self.bind(pos=self._update_bg, size=self._update_bg)

    def _update_height(self, *args):
        self.label.height = self.label.texture_size[1]
        self.time_label.height = self.time_label.texture_size[1]
        self.height = self.label.height + self.time_label.height + 34

    def _update_bg(self, *args):
        self.bg.pos = self.pos
        self.bg.size = self.size


class XPBar(Widget):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.target_progress = 0.0
        self.display_progress = 0.0
        self.gain_flash = 0.0
        self.pulse_t = 0.0
        self.levelup_flash = 0.0

        with self.canvas:
            self.outer_glow_color = Color(0.0, 0.85, 1.0, 0.10)
            self.outer_glow = RoundedRectangle(radius=[18])

            Color(0.08, 0.08, 0.12, 1)
            self.bg = RoundedRectangle(radius=[16])

            self.fill_glow_color = Color(0.0, 0.95, 1.0, 0.28)
            self.fill_glow = RoundedRectangle(radius=[16])

            self.fill_color = Color(0.0, 0.85, 1.0, 1)
            self.fill = RoundedRectangle(radius=[16])

            self.head_glow_color = Color(0.4, 1.0, 1.0, 0.0)
            self.head_glow = Ellipse(size=(18, 18), pos=(0, 0))

        self.bind(pos=self._update_bar, size=self._update_bar)
        Clock.schedule_interval(self._animate_bar, 1 / 60)

    def set_progress(self, value):
        self.target_progress = max(0.0, min(1.0, value))

    def trigger_gain(self):
        self.gain_flash = 1.0

    def trigger_levelup(self):
        self.levelup_flash = 1.0
        self.gain_flash = 1.0

    def _animate_bar(self, dt):
        diff = self.target_progress - self.display_progress
        speed = 8.0

        if abs(diff) < 0.001:
            self.display_progress = self.target_progress
        else:
            self.display_progress += diff * min(1, dt * speed)

        self.pulse_t += dt * 4.5

        if self.gain_flash > 0:
            self.gain_flash = max(0.0, self.gain_flash - dt * 1.8)

        if self.levelup_flash > 0:
            self.levelup_flash = max(0.0, self.levelup_flash - dt * 1.0)

        self._update_bar()

    def _update_bar(self, *args):
        pulse = (math.sin(self.pulse_t) + 1) / 2
        outer_alpha = 0.10 + pulse * 0.05 + self.gain_flash * 0.18 + self.levelup_flash * 0.20
        fill_glow_alpha = 0.18 + pulse * 0.12 + self.gain_flash * 0.22 + self.levelup_flash * 0.20

        self.outer_glow_color.a = min(0.55, outer_alpha)
        self.fill_glow_color.a = min(0.70, fill_glow_alpha)

        if self.levelup_flash > 0:
            self.fill_color.rgba = (0.15, 1.0, 1.0, 1)
        else:
            self.fill_color.rgba = (0.0, 0.85, 1.0, 1)

        self.outer_glow.pos = (self.x - 4, self.y - 4)
        self.outer_glow.size = (self.width + 8, self.height + 8)

        self.bg.pos = self.pos
        self.bg.size = self.size

        fill_width = max(0, self.width * self.display_progress)

        self.fill_glow.pos = self.pos
        self.fill_glow.size = (fill_width, self.height)

        self.fill.pos = self.pos
        self.fill.size = (fill_width, self.height)

        if fill_width > 6:
            head_size = 18 + self.gain_flash * 8 + self.levelup_flash * 10
            self.head_glow_color.a = min(0.95, 0.25 + self.gain_flash * 0.45 + self.levelup_flash * 0.55)
            self.head_glow.size = (head_size, head_size)
            self.head_glow.pos = (
                self.x + fill_width - head_size / 2,
                self.center_y - head_size / 2
            )
        else:
            self.head_glow_color.a = 0.0
            self.head_glow.size = (0, 0)


class SideMenu(FloatLayout):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.menu_width = min(Window.width * 0.74, 330)

        self.size_hint = (None, None)
        self.size = (0, 0)
        self.pos = (0, 0)
        self.opacity = 0

        self.overlay_btn = Button(
            size_hint=(1, 1),
            background_normal="",
            background_down="",
            background_color=(0, 0, 0, 0),
            disabled=True
        )

        with self.overlay_btn.canvas.before:
            self.overlay_color = Color(0, 0, 0, 0.0)
            self.overlay_rect = Rectangle(pos=self.overlay_btn.pos, size=self.overlay_btn.size)

        self.overlay_btn.bind(pos=self._update_overlay, size=self._update_overlay)
        self.add_widget(self.overlay_btn)

        self.panel = FloatLayout(
            size_hint=(None, 1),
            width=self.menu_width,
            pos=(-self.menu_width, 0)
        )

        with self.panel.canvas.before:
            Color(0.08, 0.08, 0.11, 0.98)
            self.panel_bg = Rectangle(pos=self.panel.pos, size=self.panel.size)

        self.panel.bind(pos=self._update_panel_bg, size=self._update_panel_bg)

        content = BoxLayout(
            orientation="vertical",
            size_hint=(1, 1),
            padding=(18, 22, 18, 22),
            spacing=18
        )

        top_bar = BoxLayout(
            size_hint=(1, None),
            height=46,
            spacing=10
        )

        title = Label(
            text="☰ Painel",
            halign="left",
            valign="middle",
            color=(1, 1, 1, 1),
            font_size=22,
            size_hint=(1, 1)
        )
        title.bind(size=lambda *x: setattr(title, "text_size", title.size))

        self.close_btn = Button(
            text="✕",
            size_hint=(None, None),
            size=(42, 42),
            background_normal="",
            background_down="",
            background_color=(0.16, 0.16, 0.20, 1),
            color=(1, 1, 1, 1),
            font_size=18
        )

        with self.close_btn.canvas.before:
            Color(0.16, 0.16, 0.20, 1)
            self.close_btn_bg = RoundedRectangle(radius=[21], pos=self.close_btn.pos, size=self.close_btn.size)

        def update_close_bg(*args):
            self.close_btn_bg.pos = self.close_btn.pos
            self.close_btn_bg.size = self.close_btn.size

        self.close_btn.bind(pos=update_close_bg, size=update_close_bg)

        top_bar.add_widget(title)
        top_bar.add_widget(self.close_btn)

        self.info_box = BoxLayout(
            orientation="vertical",
            size_hint=(1, None),
            height=240,
            spacing=12,
            padding=(0, 6, 0, 0)
        )

        self.nivel_lbl = Label(
            text="Nível: 1",
            halign="left",
            valign="middle",
            color=(0.0, 1.0, 1.0, 1),
            font_size=22,
            size_hint=(1, None),
            height=30
        )
        self.nivel_lbl.bind(size=lambda *x: setattr(self.nivel_lbl, "text_size", self.nivel_lbl.size))

        self.xp_lbl = Label(
            text="XP total: 0",
            halign="left",
            valign="middle",
            color=(1, 1, 1, 1),
            font_size=19,
            size_hint=(1, None),
            height=28
        )
        self.xp_lbl.bind(size=lambda *x: setattr(self.xp_lbl, "text_size", self.xp_lbl.size))

        self.xp_range_lbl = Label(
            text="0 / 50 neste nível",
            halign="left",
            valign="middle",
            color=(0.70, 0.85, 1, 1),
            font_size=14,
            size_hint=(1, None),
            height=22
        )
        self.xp_range_lbl.bind(size=lambda *x: setattr(self.xp_range_lbl, "text_size", self.xp_range_lbl.size))

        self.xp_bar = XPBar(
            size_hint=(1, None),
            height=20
        )

        self.status_lbl = Label(
            text="HenricoAI online",
            halign="left",
            valign="middle",
            color=(0.65, 0.85, 1, 1),
            font_size=15,
            size_hint=(1, None),
            height=24
        )
        self.status_lbl.bind(size=lambda *x: setattr(self.status_lbl, "text_size", self.status_lbl.size))

        self.info_box.add_widget(self.nivel_lbl)
        self.info_box.add_widget(self.xp_lbl)
        self.info_box.add_widget(self.xp_range_lbl)
        self.info_box.add_widget(self.xp_bar)
        self.info_box.add_widget(Widget(size_hint=(1, None), height=4))
        self.info_box.add_widget(self.status_lbl)

        self.levelup_lbl = Label(
            text="LEVEL UP",
            size_hint=(1, None),
            height=46,
            pos_hint={"center_x": 0.5, "top": 0.96},
            font_size=28,
            bold=True,
            opacity=0,
            color=(0.2, 1.0, 1.0, 1)
        )

        content.add_widget(top_bar)
        content.add_widget(self.info_box)
        content.add_widget(Widget())

        self.panel.add_widget(content)
        self.panel.add_widget(self.levelup_lbl)

        self.add_widget(self.panel)
        self.atualizar_info()

    def _update_overlay(self, *args):
        self.overlay_rect.pos = self.overlay_btn.pos
        self.overlay_rect.size = self.overlay_btn.size

    def _update_panel_bg(self, *args):
        self.panel_bg.pos = self.panel.pos
        self.panel_bg.size = self.panel.size

    def mostrar_levelup(self):
        self.levelup_lbl.opacity = 0
        self.levelup_lbl.y = self.panel.height - 90

        Animation.cancel_all(self.levelup_lbl)
        anim = (
            Animation(opacity=1, y=self.panel.height - 110, d=0.18, t="out_quad") +
            Animation(opacity=1, d=0.35) +
            Animation(opacity=0, y=self.panel.height - 130, d=0.30, t="in_quad")
        )
        anim.start(self.levelup_lbl)

    def atualizar_info(self, trigger_gain=False, level_up=False):
        nivel = progresso["nivel"]
        xp_total = progresso["xp"]

        inicio = xp_inicio_nivel(nivel)
        limite = xp_limite_por_nivel(nivel)

        xp_no_nivel = xp_total - inicio
        faixa = max(1, limite - inicio)
        progresso_barra = xp_no_nivel / faixa

        self.nivel_lbl.text = f"Nível: {nivel}"
        self.xp_lbl.text = f"XP total: {xp_total}"
        self.xp_range_lbl.text = f"{xp_no_nivel} / {faixa} neste nível"
        self.xp_bar.set_progress(progresso_barra)

        if trigger_gain:
            self.xp_bar.trigger_gain()

        if level_up:
            self.xp_bar.trigger_levelup()
            self.mostrar_levelup()

    def open_menu(self):
        self.atualizar_info()
        self.size_hint = (1, 1)
        self.size = Window.size
        self.opacity = 1
        self.overlay_btn.disabled = False

        Animation.cancel_all(self.panel)
        Animation.cancel_all(self.overlay_color)

        Animation(x=0, d=0.18, t="out_quad").start(self.panel)
        Animation(a=0.45, d=0.18).start(self.overlay_color)

    def close_menu(self):
        Animation.cancel_all(self.panel)
        Animation.cancel_all(self.overlay_color)

        anim_panel = Animation(x=-self.menu_width, d=0.18, t="out_quad")
        anim_overlay = Animation(a=0.0, d=0.18)

        def fim(*_):
            self.overlay_btn.disabled = True
            self.opacity = 0
            self.size_hint = (None, None)
            self.size = (0, 0)

        anim_panel.bind(on_complete=fim)
        anim_panel.start(self.panel)
        anim_overlay.start(self.overlay_color)


# =========================
# SPLASH
# =========================
class Splash(Screen):
    def on_enter(self):
        self.t = 0
        self.transition_started = False
        self.frase_atual = random.choice(FRASES_SISTEMA + FRASES_MOTIVACIONAIS)

        with self.canvas:
            Color(0, 0, 0, 1)
            self.bg = Rectangle(size=self.size, pos=self.pos)
            Color(0, 0.45, 1, 0.16)
            self.glow_outer = Line(circle=(0, 0, 280), width=8)
            Color(0, 0.65, 1, 0.10)
            self.glow_mid = Line(circle=(0, 0, 220), width=6)
            Color(0, 0.9, 1, 0.95)
            self.r1 = Line(circle=(0, 0, 120), width=2.2)
            Color(0, 0.75, 1, 0.75)
            self.r2 = Line(circle=(0, 0, 180), width=1.8)
            Color(0, 0.55, 1, 0.55)
            self.r3 = Line(circle=(0, 0, 240), width=1.2)
            Color(0, 1, 1, 0.95)
            self.core_ring = Line(circle=(0, 0, 40), width=3)
            Color(0, 1, 1, 0.20)
            self.core_fill = Ellipse(size=(36, 36), pos=(0, 0))
            Color(0, 1, 1, 0.75)
            self.scan_line = Line(points=[0, 0, 0, 0], width=1.5)
            Color(0, 0.8, 1, 0.22)
            self.cross_h = Line(points=[0, 0, 0, 0], width=1)
            self.cross_v = Line(points=[0, 0, 0, 0], width=1)
            Color(0, 1, 1, 0.9)
            self.dot1 = Ellipse(size=(8, 8), pos=(0, 0))
            self.dot2 = Ellipse(size=(10, 10), pos=(0, 0))
            self.dot3 = Ellipse(size=(6, 6), pos=(0, 0))
            Color(0, 0.75, 1, 1)
            self.cover = Ellipse(size=(0, 0), pos=(0, 0))

        self.label = Label(text="Inicializando HenricoAI...", font_size=28, pos_hint={"center_x": 0.5, "center_y": 0.22}, color=(1, 1, 1, 1))
        self.add_widget(self.label)

        self.sub_label = Label(text="💡 " + self.frase_atual, font_size=16, pos_hint={"center_x": 0.5, "center_y": 0.16}, color=(0.7, 0.7, 0.7, 1))
        self.add_widget(self.sub_label)

        self.anim_event = Clock.schedule_interval(self.animar, 1 / 60)
        Clock.schedule_once(self.segundo_texto, 3)

    def animar(self, dt):
        if self.transition_started:
            return

        self.t += dt * 2.2
        self.bg.size = self.size
        self.bg.pos = self.pos

        cx = self.width / 2
        cy = self.height / 2
        pulse = math.sin(self.t * 2.4) * 6

        self.r1.circle = (cx, cy, 120 + pulse)
        self.r2.circle = (cx, cy, 180 + pulse * 0.7)
        self.r3.circle = (cx, cy, 240 + pulse * 0.4)
        self.glow_mid.circle = (cx, cy, 220 + pulse * 1.2)
        self.glow_outer.circle = (cx, cy, 280 + pulse * 1.5)
        self.core_ring.circle = (cx, cy, 40 + math.sin(self.t * 3) * 2)

        core_size = 36 + math.sin(self.t * 3.2) * 2
        self.core_fill.size = (core_size, core_size)
        self.core_fill.pos = (cx - core_size / 2, cy - core_size / 2)

        self.cross_h.points = [cx - 260, cy, cx + 260, cy]
        self.cross_v.points = [cx, cy - 260, cx, cy + 260]

        angle = self.t * 1.8
        end_x = cx + math.cos(angle) * 250
        end_y = cy + math.sin(angle) * 250
        self.scan_line.points = [cx, cy, end_x, end_y]

        self.dot1.pos = (cx + math.cos(self.t * 1.8) * 120 - 4, cy + math.sin(self.t * 1.8) * 120 - 4)
        self.dot2.pos = (cx + math.cos(self.t * -1.4) * 180 - 5, cy + math.sin(self.t * -1.4) * 180 - 5)
        self.dot3.pos = (cx + math.cos(self.t * 2.4) * 240 - 3, cy + math.sin(self.t * 2.4) * 240 - 3)

    def segundo_texto(self, dt):
        self.label.text = "Sistema Online..."
        self.sub_label.text = "💡 " + random.choice(FRASES_SISTEMA)
        Clock.schedule_once(self.terceiro_texto, 2)

    def terceiro_texto(self, dt):
        self.label.text = "Me faça uma pergunta..."
        self.sub_label.text = "💡 " + random.choice(FRASES_MOTIVACIONAIS)
        Clock.schedule_once(self.expandir_inicio, 2)

    def expandir_inicio(self, dt):
        self.transition_started = True
        if hasattr(self, "anim_event"):
            self.anim_event.cancel()

        self.r1.circle = (0, 0, 0)
        self.r2.circle = (0, 0, 0)
        self.r3.circle = (0, 0, 0)
        self.glow_mid.circle = (0, 0, 0)
        self.glow_outer.circle = (0, 0, 0)
        self.core_ring.circle = (0, 0, 0)
        self.scan_line.points = [0, 0, 0, 0]
        self.cross_h.points = [0, 0, 0, 0]
        self.cross_v.points = [0, 0, 0, 0]

        self.core_fill.size = (0, 0)
        self.dot1.size = (0, 0)
        self.dot2.size = (0, 0)
        self.dot3.size = (0, 0)

        self.label.opacity = 0
        self.sub_label.opacity = 0

        self.cover_radius = 40
        self.expand_event = Clock.schedule_interval(self.expandir, 1 / 60)

    def expandir(self, dt):
        self.cover_radius += 55
        cx = self.width / 2
        cy = self.height / 2
        d = self.cover_radius * 2

        self.cover.size = (d, d)
        self.cover.pos = (cx - self.cover_radius, cy - self.cover_radius)

        if self.cover_radius > max(self.width, self.height) * 1.5:
            if hasattr(self, "expand_event"):
                self.expand_event.cancel()
            self.manager.transition = NoTransition()
            self.manager.current = "chat"
            return False


# =========================
# CHAT
# =========================
class Chat(Screen):
    def on_enter(self):
        if not hasattr(self, "entrada"):
            self.build_chat()

    def abrir_menu_lateral(self, *args):
        self.side_menu.open_menu()
        Animation.cancel_all(self.float_menu_btn)
        Animation(x=self.side_menu.menu_width + 10, d=0.18, t="out_quad").start(self.float_menu_btn)

    def fechar_menu_lateral(self, *args):
        self.side_menu.close_menu()
        Animation.cancel_all(self.float_menu_btn)
        Animation(x=14, d=0.18, t="out_quad").start(self.float_menu_btn)

    def build_chat(self):
        self.root_float = FloatLayout()

        self.bg_network = ParticleNetwork()
        self.bg_network.opacity = 1
        self.root_float.add_widget(self.bg_network)

        root = BoxLayout(orientation="vertical", padding=(12, 14, 12, 10), spacing=12)

        topo_box = AnchorLayout(size_hint=(1, None), height=72)

        self.top_capsula = CapsuleBar(
            orientation="horizontal",
            size_hint=(None, None),
            size=(260, 58),
            padding=(12, 0),
            spacing=10
        )

        core_wrap = AnchorLayout(size_hint=(None, None), size=(52, 52))
        self.top_core = AnimatedCore(size_hint=(None, None), size=(52, 52))
        core_wrap.add_widget(self.top_core)

        titulo = Label(text="HenricoAI", font_size=22, bold=True, color=(1, 1, 1, 1))

        self.top_capsula.add_widget(core_wrap)
        self.top_capsula.add_widget(titulo)
        topo_box.add_widget(self.top_capsula)
        root.add_widget(topo_box)

        self.scroll = ScrollView(size_hint=(1, 1), do_scroll_x=False, bar_width=3)
        self.messages_anchor = AnchorLayout(anchor_x="center", anchor_y="top", size_hint_y=None)

        self.chat_layout = GridLayout(cols=1, spacing=12, size_hint_y=None, padding=(2, 8, 2, 10))
        self.chat_layout.bind(minimum_height=self.chat_layout.setter("height"))

        self.messages_anchor.add_widget(self.chat_layout)
        self.scroll.add_widget(self.messages_anchor)

        self.scroll.bind(height=self.update_messages_anchor_height)
        self.chat_layout.bind(height=self.update_messages_anchor_height)

        root.add_widget(self.scroll)

        bottom_box = AnchorLayout(size_hint=(1, None), height=78, anchor_x="center", anchor_y="center")

        barra = CapsuleBar(
            orientation="horizontal",
            size_hint=(1, None),
            height=64,
            padding=(12, 8),
            spacing=8
        )

        self.entrada = TextInput(
            multiline=False,
            hint_text="Digite sua pergunta...",
            size_hint=(1, 1),
            background_normal="",
            background_active="",
            background_color=(0, 0, 0, 0),
            foreground_color=(1, 1, 1, 1),
            hint_text_color=(0.65, 0.65, 0.70, 1),
            cursor_color=(0.0, 0.85, 1.0, 1),
            padding=(12, 14, 12, 14),
            font_size=18,
            write_tab=False
        )
        self.entrada.bind(on_text_validate=self.enviar)

        botao_wrap = AnchorLayout(size_hint=(None, 1), width=58)

        botao = Button(
            text="➤",
            size_hint=(None, None),
            size=(46, 46),
            background_normal="",
            background_down="",
            background_color=(0.06, 0.56, 0.95, 1),
            color=(1, 1, 1, 1),
            font_size=20
        )
        botao.bind(on_press=self.enviar)

        with botao.canvas.before:
            Color(0.06, 0.56, 0.95, 1)
            botao.bg = RoundedRectangle(radius=[23], pos=botao.pos, size=botao.size)

        def update_btn_bg(*args):
            botao.bg.pos = botao.pos
            botao.bg.size = botao.size

        botao.bind(pos=update_btn_bg, size=update_btn_bg)

        botao_wrap.add_widget(botao)
        barra.add_widget(self.entrada)
        barra.add_widget(botao_wrap)
        bottom_box.add_widget(barra)
        root.add_widget(bottom_box)

        self.root_float.add_widget(root)

        self.side_menu = SideMenu()
        self.root_float.add_widget(self.side_menu)
        self.side_menu.overlay_btn.bind(on_press=self.fechar_menu_lateral)
        self.side_menu.close_btn.bind(on_press=self.fechar_menu_lateral)
        self.side_menu.close_menu()

        self.float_menu_btn = Button(
            text="☰",
            size_hint=(None, None),
            size=(50, 50),
            background_normal="",
            background_down="",
            background_color=(0, 0, 0, 0),
            color=(1, 1, 1, 1),
            font_size=24,
            pos=(14, self.height - 68)
        )
        self.float_menu_btn.bind(on_press=self.abrir_menu_lateral)

        with self.float_menu_btn.canvas.before:
            self.float_btn_glow = Color(0.08, 0.55, 1.0, 0.18)
            self.float_btn_glow_shape = RoundedRectangle(radius=[25], pos=self.float_menu_btn.pos, size=self.float_menu_btn.size)
            Color(0.12, 0.12, 0.14, 0.98)
            self.float_btn_bg = RoundedRectangle(radius=[25], pos=self.float_menu_btn.pos, size=self.float_menu_btn.size)

        def update_float_btn(*args):
            self.float_btn_bg.pos = self.float_menu_btn.pos
            self.float_btn_bg.size = self.float_menu_btn.size
            self.float_btn_glow_shape.pos = (self.float_menu_btn.x - 4, self.float_menu_btn.y - 4)
            self.float_btn_glow_shape.size = (self.float_menu_btn.width + 8, self.float_menu_btn.height + 8)

        self.float_menu_btn.bind(pos=update_float_btn, size=update_float_btn)
        self.root_float.add_widget(self.float_menu_btn)

        self.add_widget(self.root_float)

        self.root_float.bind(size=self.update_layouts, pos=self.update_layouts)
        self.bg_network.size = self.root_float.size
        self.bg_network.pos = self.root_float.pos

        self.typing_row = None

        self.add_msg("Olá Henry.", is_user=False)
        self.add_msg("Faça uma pergunta.", is_user=False)

    def update_layouts(self, *args):
        if hasattr(self, "bg_network"):
            self.bg_network.size = self.root_float.size
            self.bg_network.pos = self.root_float.pos

        if hasattr(self, "side_menu") and self.side_menu.size_hint == (1, 1):
            self.side_menu.size = self.root_float.size
            self.side_menu.pos = self.root_float.pos

        if hasattr(self, "float_menu_btn"):
            self.float_menu_btn.y = self.height - 68
            if self.float_menu_btn.x < 14:
                self.float_menu_btn.x = 14

    def hora_atual(self):
        return datetime.now().strftime("%H:%M")

    def update_messages_anchor_height(self, *args):
        if hasattr(self, "scroll") and hasattr(self, "messages_anchor") and hasattr(self, "chat_layout"):
            self.messages_anchor.height = max(self.scroll.height, self.chat_layout.height)
            self.messages_anchor.size_hint_y = None

    def add_msg(self, texto, is_user=False):
        linha = AnchorLayout(anchor_x="right" if is_user else "left", size_hint_y=None, padding=(4, 2, 4, 2))
        bubble = BubbleBox(text=texto, is_user=is_user, time_text=self.hora_atual(), size_hint=(None, None))

        def ajustar_largura(*args):
            bubble.width = max(140, min(self.width * 0.70, 420))

        ajustar_largura()
        self.bind(width=ajustar_largura)

        linha.height = bubble.height + 6
        bubble.bind(height=lambda *x: setattr(linha, "height", bubble.height + 6))

        linha.add_widget(bubble)
        self.chat_layout.add_widget(linha)

        self.update_messages_anchor_height()
        Clock.schedule_once(lambda dt: setattr(self.scroll, "scroll_y", 0))

        if not is_user:
            if hasattr(self, "top_core"):
                self.top_core.flash()
            if hasattr(self, "top_capsula"):
                self.top_capsula.flash()
            if hasattr(self, "bg_network"):
                self.bg_network.trigger_pulse()

    def mostrar_digitando(self):
        if self.typing_row is not None:
            return

        if hasattr(self, "top_core"):
            self.top_core.set_active(True)

        self.typing_row = AnchorLayout(anchor_x="left", size_hint_y=None, padding=(4, 2, 4, 2))
        bubble = BubbleBox(text="HenricoAI está digitando...", is_user=False, time_text=self.hora_atual(), size_hint=(None, None))
        bubble.width = max(180, min(self.width * 0.62, 360))

        self.typing_row.height = bubble.height + 6
        bubble.bind(height=lambda *x: setattr(self.typing_row, "height", bubble.height + 6))

        self.typing_row.add_widget(bubble)
        self.chat_layout.add_widget(self.typing_row)

        self.update_messages_anchor_height()
        Clock.schedule_once(lambda dt: setattr(self.scroll, "scroll_y", 0))

    def remover_digitando(self):
        if self.typing_row is not None:
            if self.typing_row in self.chat_layout.children:
                self.chat_layout.remove_widget(self.typing_row)
            self.typing_row = None
            self.update_messages_anchor_height()

        if hasattr(self, "top_core"):
            self.top_core.set_active(False)

    def responder_com_delay(self, pergunta):
        resposta = responder(pergunta)
        self.remover_digitando()
        self.add_msg(resposta, is_user=False)

    def enviar(self, instance):
        pergunta = self.entrada.text.strip()
        if not pergunta:
            return

        self.add_msg(pergunta, is_user=True)

        subiu = ganhar_xp(5)
        if hasattr(self, "side_menu"):
            self.side_menu.atualizar_info(trigger_gain=True, level_up=subiu)

        if subiu:
            self.add_msg(f"Sistema: Você subiu para o nível {progresso['nivel']} 🚀", is_user=False)

        self.entrada.text = ""
        self.entrada.focus = False

        self.mostrar_digitando()
        Clock.schedule_once(lambda dt: self.responder_com_delay(pergunta), 0.9)


# =========================
# APP
# =========================
class HenricoAI(App):
    def build(self):
        sm = ScreenManager(transition=NoTransition())
        sm.add_widget(Splash(name="splash"))
        sm.add_widget(Chat(name="chat"))
        return sm


if __name__ == "__main__":
    HenricoAI().run()