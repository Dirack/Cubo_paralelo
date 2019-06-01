#!/usr/bin/env python
# -*- coding: utf-8 -*-
#
# Madagascar-Mcubo  (Script do Madagascar)
#
# Objetivo: Versão paralelizada do programa sfcubo. Gera um cubo de coerência dos
# parâmetros do CRS zero offset (RN,RNIP e BETA) a partir de um cubo de dados modelados
# A(m,h,t) (CMP, Offset, tempo)
#
# Site: http://www.dirackslounge.online
# 
# Versão 1.1 - temp0 e c0 são fornecidos pelo usuário
#
# Programador: Rodolfo A. C. Neves 01/06/2019
#
# email: rodolfo_profissional@hotmail.com
#
# Funcionamento: digite 'scons' no terminal e aperte enter.
#
# Licensa:
#  		Copyright (C) 2018 grupo de programação aplicada à geofísica (GPGEOF)
#  		da Universidade Federal do Pará (UFPA); Belém, Pará, Brasil. 
#
#  		Esse programa é um software livre; você pode redistribuir e/ou modificar
#  		sobre os termos da licensa pública geral (LPG) publicada pela Free 
#  		Software Foundation; na versão 2 da licensa, ou (a seu critério) qualquer
#  		versão posterior.
#
#  		Este programa é distribuído na esperança que será útil, mas SEM NENHUMA
#  		GARANTIA; nem mesmo a garantia implícita de MERCANTILIDADE ou SERVENTIA
#  		A UM PROPÒSITO DETERMINADO. veja a LPG licensa pública geral para mais
#  		detalhes.
#
#  		Você deve ter recebido uma cópia da LPG licensa pública geral junto com
#  		este programa; se não, escreva para a Free Software Foundation, Inc., 
#  		51 Franklin Street, Quinquagésimo andar, Boston, MA  02110-1301, USA.


# Importe a Biblioteca do madagascar 
from rsf.proj import *

# Biblioteca matemática do python
import math 

#------------------------{ Modelagem }-----------------------
# Cria um refletor gaussiano e o modelo de velocidades v(z)
# em que a velocidade cresce linearmente com a profundidade.
# Também cria o arquivo dip.rsf que é a inclinação local do
# refletor (necessária para a modelagem kirchoff).
#------------------------------------------------------------

# Criar o refletor a partir de uma função gaussiana
Flow('dome',None,
     '''
     math d1=0.01 n1=2001 o1=-5 unit1=km label1=Afastamento
     output="4-3*exp(-(x1-5)^2/9)"
     ''')

# TODO Comentários
for g in range(2):
    dome = 'dome%d' % g
    Plot(dome,'dome',
         '''
         graph min2=0 max2=4 min1=0 max1=10
         yreverse=y plotcol=%d plotfat=%d
         wantaxis=n wanttitle=n scalebar=y pad=n
         ''' % ((7,0)[g],(7,3)[g]))

#--------{ Modelo de velocidades } ---------
# Montar um modelo de velocidades v(z), 
# velocidade cresce com a profundidade
# v_0 é 1.5 (Km/s)
# 0.5 Gradiente de velocidade
#-------------------------------------------
Flow('vel','dome',
     '''
     window min1=0 max1=10 |
     spray axis=1 n=451 d=0.01 o=0 label=Profundidade unit=km |
     math output="1.5+0.5*x1+0.0*x2"
     ''')

Plot('vel',
     '''
     grey color=j allpos=y bias=1.5 scalebar=y wanttitle=n
     barreverse=y barlabel=Velocidade barunit=km/s
     ''')

Result('dome','vel dome0 dome1','Overlay')

# dip.rsf é a inclinação do refletor gaussiano (derivada)
Flow('dip','dome','math output="2/3*(x1-5)*input" ')

#---------------{ Modelagem Kirchoff }--------------------
# Unidades de medida (Km, s, Km/s)
#	h é o meio afastamento, m é o CMP
# 	nh número de receptores 
# 	dh incremento entre os receptores
# 	h0 orige dos receptores
# 	ns número de fontes
# 	ds incremento entre as fontes
# 	s0 origem das fontes
#---------------------------------------------------------
Flow('data','dome dip',
     '''
     kirmod cmp=y dip=${SOURCES[1]} 
     nh=161 dh=0.025 h0=0
     ns=401 ds=0.025 s0=0
     freq=10 dt=0.004 nt=1001
     vel=1.5 gradz=0.5 gradx=0.0 verb=y |
     put d2=0.0125
     ''')

Result('data',
       '''
       byte |
       transp plane=23 |
       grey3 flat=n frame1=500 frame3=80 frame2=200
       label1=Tempo unit1=s 
       label3=Meio-afastamento unit3=km 
       label2=PMC unit2=km
       title='Dados Modelo refletor gaussiano' point1=0.8 point2=0.8 
       ''')

Plot('data',
       '''
       byte |
       transp plane=23 memsize=1000 |
       grey3 flat=y frame1=500 frame3=80 frame2=200
       label1=Tempo unit1=s 
       label3=Meio-afastamento unit3=km 
       label2=PMC unit2=km
       title='Dados modelo refletor gaussiano' point1=0.8 point2=0.8 
       ''')


# Gerar cubo de coerência com sfPcubo
Flow('cubo_out','data',
	'''
	Pcubo m0=5 verb=1
	''')

Result('cubo_out',
	'''
	byte |
	grey3 movie=1 color=j yreverse=y xreverse=n
	title='Dados Modelo refletor gaussiano'
	''')

End()
