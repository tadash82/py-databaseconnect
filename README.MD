[Acesso a Banco de dados SQL
O Python não possui acesso nativo a bancos de dados SQL em sua biblioteca padrão, somente ao Berkley database engine(BDB), mas define uma API padrão que os drivers de acesso a esses bancos de dados devem seguir, assim qualquer que seja o banco de dados os procedimentos são parecidos.

Os quatro principais bancos de dados livres que podem ser acessados do Python são o SQLite, o MySQL, o PostgreSQL e o Interbase/Firebird.

Para cada um inicie importando os drivers:

Esconder número das linhas
import MySQLdb # para o MySQL
import sqlite # para o SQLite
from pyPgSQL import PgSQL # para o PostgreSQL
import kinterbasdb # para Interbase / Firebird
import pymssql #para o MS-SQL. (existem outros módulos - ADOdb for Python/PHP)
import cx_Oracle #para o Oracle
Nota: Não faça from driver import \* porque isso fará com que o Python importe muito lixo para o namespace padrão, além disso, isso impossibilita o uso de mais de um driver ao mesmo tempo.

Conexão
Aqui os estilos variam muito pouco, com excessão do SQLite, que acessa um arquivo local onde está o banco de dados.

Para o MySQL:

Esconder número das linhas
con = MySQLdb.connect('servidor', 'usuario', 'senha')
con.select_db('banco de dados')
Para o SQLite:

Esconder número das linhas
con = sqlite.connect('nome do arquivo', mode=775) # no futuro # mode definirá o modo # de trabalho
Para o PostgreSQL:

Esconder número das linhas
con = PgSQL.connect(host='servidor',
database='banco de dados',
user='usuario',
password='senha')
Para o Interbase / Firebird:

Esconder número das linhas
con = kinterbasdb.connect(dsn='servidor:/path/arquivo.fdb',
user='usuario',
password='senha')
Para o MS-SQL

Esconder número das linhas
con = pymssql.connect(host = 'servidor',
user = 'usuario',
password = 'senha',
database = 'base de dados')
Para o Oracle

Esconder número das linhas
1 con = cx_Oracle.connect('usuario/senha@tnsname')
Uso
Daqui pra frente todos os métodos da API são iguais.

Obtendo uma transação (cursor)

Esconder número das linhas
cursor = con.cursor()
Executando um sql

Esconder número das linhas
cursor.execute('ALGUM SQL')
É importante notar que para colocar parâmetros em sql a melhor forma é fazer de acordo com a API python, isto é, da seguinte forma:

Esconder número das linhas
cursor.execute('INSERT INTO TABELA (CAMPO1, CAMPO2, CAMPO3) VALUES (?,?,?)', (valor1, valor2, valor3))
As variáveis valor1, valor2 e valor3 devem ser de um tipo compatível com a coluna correspondente do banco no sql. Isso porque ao passar parâmetros para ao sql dessa forma, você estará deixando a cargo do módulo de conexão ao seu banco o tratamento dos valores. Por exemplo, se valor1 for uma string, o módulo automaticamente colocará o valor entre aspas no lugar de ?. Se valor2 for uma data (datetime.datetime), o módulo convertela-á para o formato do banco. Isso é útil também para evitar sql injections.

Note também que usamos o caracter ? para representar o lugar onde um valor deve ser colocado pelo cursor.execute(). Esse caracter depende do módulo que você utilizar para conectar ao banco de dados, e essa propriedade chama-se paramstyle. Verifique este item na documentação do módulo ao começar a utlizá-lo.

Salvando as alterações
Depois de fazer um insert, update, ou delete, deveremos chamar o método commit() da conexão, para que as mudanças tenham efeito no banco.

Esconder número das linhas
cursor.execute('INSERT INTO TABELA (CAMPO1, CAMPO2, CAMPO3) VALUES (?,?,?)', (valor1, valor2, valor3))
con.commit()
Obtendo o resultado

Esconder número das linhas
rs = cursor.fetchone() # busca uma linha ou;
rs = cursor.fetchall() # busca todas as linhas ou;
rs = cursor.dictfetchall() # busca todas as linhas, # cada linha tem um dicionário # com os nomes dos campos
O KInterbasDB (Interbase/Firebird) não possui suporte para o método: dictfetchall()

O MS-SQL também não possuí suporte para o método: dictfetchall()

E no resultado estarão os campos:

Esconder número das linhas
print(rs[0]) # valor da primeira coluna.
Alternativamente você pode usar o cursor como um iterator:

Esconder número das linhas
for linha in cursor:
print linha[0] # valor da primeira coluna
Notas a respeito do Banco de dados
SQLite: Eu uso pra quase tudo, pelo menos para aquilo que não precise ser partilhado em rede. É rapido, simples, não depende de servidor (já que o seu programa fica responsável pelo gerenciamento do banco de dados). Não serve para nada com intenções mais sérias já que não funciona em rede (a não ser que se partilhe os arquivos e acesse-o a partir daí, mas ele não é multiusuário).

MySQL: É o meu preferido. Fácil de instalar (principalmente no windows), fácil de gerenciar e bastante rápido. É bastante eficiente e é o mais utilizado na internet em sites dinâmicos.

PostgreSQL: É mais difícil de instalar e configurar, o driver para Python é dificil de compilar e tem padrões esquisitos (Eu compilei a partir dos fontes, mesmo assim o driver não encontrava as bibliotecas necessárias para a compilação). Eu pessoalmente não sei porque alguem usa isso, mas.... Nos testes que eu vi é o unico que tem condições de competir com criaturas como o Oracle. Existem diversos drivers para PostgreSQL e Python, os dois melhores são pyPgSQL tratado no artido e ainda psycopg, IMHO o melhor disparado. Os comentários acima não se aplicam ao driver do psycopg, que é muito fácil de instalar, rápido e estável.

Interbase/Firebird: Fácil de instalar no Microsoft Windows a partir dos binários, porém, compilar a partir dos fontes não é tão fácil assim. O banco é bastante robusto, e está em pé de igualdade com o PostgreSQL em termos de recursos. Muito utilizado em Borland Delphi. Os arquivos de instalação podem ser encontrados aqui.

MS-SQL: BD comercial da Microsoft, fácil configuração e tudo na mão através da Interface gráfica. Quanto a performace não tenho uma base de comparação em produção (ainda). Mas ele atende as nossas necessidades ;).

Bancos de Dados Proprietários](https://wiki.python.org.br/BancosDeDadosSql#:~:text=Acesso%20a%20Banco,de%20Dados%20Propriet%C3%A1rios)
