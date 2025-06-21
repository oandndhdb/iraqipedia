
# iraqipedia
from flask import Flask, render_template, request import sqlite3 import os

app = Flask(name)

قاعدة البيانات

DATABASE = 'iraq.db'

إنشاء قاعدة البيانات إذا ما موجودة

if not os.path.exists(DATABASE): conn = sqlite3.connect(DATABASE) conn.execute(''' CREATE TABLE sections ( id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT UNIQUE NOT NULL, title TEXT NOT NULL, content TEXT NOT NULL ); ''') sample_data = [ ('provinces', 'المحافظات العراقية', 'معلومات عن كل محافظة من محافظات العراق.'), ('history', 'تاريخ العراق', 'العراق مهد الحضارات منذ آلاف السنين...'), ('politics', 'السياسة', 'العراق جمهورية برلمانية...'), ('oil', 'النفط والطاقة', 'العراق من أكبر الدول المصدّرة للنفط...'), ('food', 'الأكلات الشعبية', 'من أشهر الأكلات العراقية: الدولمة، البرياني، القيمر...'), ('religions', 'الأديان والمذاهب', 'يوجد في العراق أغلبية مسلمة مع وجود مسيحيين وصابئة وأيزيديين...'), ('personalities', 'الشخصيات العراقية', 'أشهر الشخصيات: عبد الكريم قاسم، صدام حسين، نوري المالكي...'), ('language', 'اللغة واللهجات', 'العربية والكردية هما اللغتان الرسميتان...'), ('modern-events', 'الأحداث الحديثة', 'التظاهرات، التغيرات السياسية، الانتخابات...'), ('general', 'معلومات عامة', 'العراق يقع في غرب آسيا، عاصمته بغداد...') ] conn.executemany('INSERT INTO sections (name, title, content) VALUES (?, ?, ?)', sample_data) conn.commit() conn.close()

def get_db_connection(): conn = sqlite3.connect(DATABASE) conn.row_factory = sqlite3.Row return conn

@app.route('/') def index(): conn = get_db_connection() sections = conn.execute('SELECT name, title FROM sections').fetchall() conn.close() return ''' <html> <head><title>ويكي العراق</title><meta charset="UTF-8"></head> <body style="font-family: Arial; direction: rtl;"> <h1>أهلاً بك في موسوعة العراق 🇮🇶</h1> <ul> %s </ul> </body> </html> ''' % ''.join([f'<li><a href="/section/{s["name"]}">{s["title"]}</a></li>' for s in sections])

@app.route('/section/<name>') def section(name): conn = get_db_connection() content = conn.execute('SELECT * FROM sections WHERE name = ?', (name,)).fetchone() conn.close() if content: return f''' <html> <head><title>{content["title"]}</title><meta charset="UTF-8"></head> <body style="font-family: Arial; direction: rtl;"> <h1>{content["title"]}</h1> <p>{content["content"]}</p> <a href="/">الرجوع للرئيسية</a> </body> </html> ''' return "القسم غير موجود", 404

if name == 'main': app.run(debug=True)

