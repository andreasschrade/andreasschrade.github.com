---
layout: post
title: "Android: SQLite-Integration Mithilfe von SQLiteOpenHelper realisieren"
category: "android"
---



## Hintergrund

SQLite ist ein essentieller Bestandteil der Android-Platform und dient generell zur Speicherung aller App-bezogenen Daten.
Die Hilfsklasse <em>SQLiteOpenHelper</em> vereinfacht den Umgang mit SQLite-Datenbanken vor allem im Zusammenhang mit der Datenbank-Erzeugung sowie dem Versions-Management.
Wie sich die Hilfklassen in die eigene App integrieren lässt, ist im folgenden Abschnitt beschrieben.
 
### Vorgehensweise

Um sich die Hilfsklasse zunutze zu machen, gilt es einen individuellen Subtypen (MyDBOpenHelper )zu erstellen.
Dieser beinhaltet das SQL-Query zu Erzeugung der Datenbank bzw. der Tabellen und ebenso die Implementierung von Datenbankupdates bzw. Schema-Migrationen.

Die eigene SQLiteHelper-Implementierung wird im Beispiel als statische innere-Klasse der High-Level-Hilfklasse (ExampleDBHelper) realisiert.
Diese High-Level Hilfsklasse kann beispielsweise von Activites genutzt werden, um Tabelleneinträge zu erzeugen oder zu selektieren.

Die Beispielimplementierung:
 
{% highlight java %}
public class ExampleDBHelper {

	private MyDBOpenHelper dbHelper;  // Referenz auf eigene SQLiteHelper-Implementierung
	private SQLiteDatabase database;
	public final static String USER_TABLE = "User"; // Tabellenname

	public final static String USER_ID = "id"; // PK
	public final static String USER_NAME = "name";  // Spalte "name"
	public final static String USER_AGE = "age"; // Spalte "age"

	public ExampleDBHelper(Context context) {
		dbHelper = new MyDBOpenHelper(context);   // Helperklasse instanziieren
		database = dbHelper.getWritableDatabase();  // Referenz auf Datenbank beziehen
	}

	// Erzeugung von User-Records
	public long createRecords(String id, String name, int age) {
		ContentValues values = new ContentValues();
		values.put(USER_ID, id);
		values.put(USER_NAME, name);
		values.put(USER_AGE, age);
		return database.insert(USER_TABLE, null, values);
	}

  // Selektion von User-Records
	public Cursor selectRecords() {
		String[] cols = new String[] { USER_ID, USER_NAME, USER_AGE };
		Cursor mCursor = database.query(true, USER_TABLE, cols, null, null,
				null, null, null, null);
		if (mCursor != null) {
			mCursor.moveToFirst();
		}
		return mCursor;
	}

	private static class MyDBOpenHelper extends SQLiteOpenHelper {

		private static final String DATABASE_NAME = "ExampleDb.db";
		private static final int DATABASE_VERSION = 1;

		// SQL Statement zur Anlage der Tabelle
		private static final String DATABASE_CREATE = "create table "
				+ USER_TABLE + " (" + USER_ID
				+ " integer primary key autoincrement, " + USER_NAME
				+ " text not null, " + USER_AGE + " integer);";

		public MyDBOpenHelper(Context context) {
			super(context, DATABASE_NAME, null, DATABASE_VERSION);
		}

		// Wird aufgerufen, wenn noch keine (persistierte) Datenbank existiert
		// und erzeugt entsprechend mithilfe des SQL-Create-Statements die
		// notwendige Tabellen-Struktur
		@Override
		public void onCreate(SQLiteDatabase db) {
			db.execSQL(DATABASE_CREATE);
		}

		// Wird aufgerufen, wenn die persistierte Datenbank eine Schema-Änderung
		// erfordert.
		// Das bedeutet, dass in Folge eines App-Updates die bestehende
		// Tabellen-Struktur auf die neue Struktur angepasst werden muss
		@Override
		public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
			if (oldVersion < 2) {
				// Upgrade von Version 1 auf 2 durchführen
				// -> Tabellenspalte "country" hinzufügen
				db.execSQL("ALTER TABLE " + USER_TABLE + " ADD COLUMN country text");
			}

			if (oldVersion < 3) {
				// Upgrade von Version 2 auf 3 durchführen
				// -> Tabellenspalte "city" hinzufügen
				db.execSQL("ALTER TABLE " + USER_TABLE + " ADD COLUMN city text");
			}

		}

	}
}
{% endhighlight %}

Im Beispiel werden zwei Schemaänderungen an der User-Tabelle vorgenommen.
Für das Update von Version 1 auf 2 wird via ALTER-Statement die Spalte "country" hinzugefügt.
Das Update von Version 2 auf 3 birgt dagegen eine neue Spalte namens "city".

Denkbar ist an dieser Stelle auch, dass schlichtweg die Tabelle gelöscht wird. Es hängt eben somit ganz vom individuellen Anwendungsfall ab, wie die Migration ablaufen muss.
