using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Windows.Forms;
using Microsoft.VisualBasic.PowerPacks; //Çizgi çizme kütüphanesi
using System.IO; //.txt yazma kütüphanesi
namespace koordinat_belirle
{
    public partial class Form1 : Form
    {
        Button iptalbut = new Button(); // İptal buttonu oluşturma
        Button onaylabut=new Button(); // Onaylama buttonu oluşturma
        ShapeContainer canvas = new ShapeContainer();//Çizim sınıfı (LineShape için)
        int engelsay = 0; //Engel sayım değişkeni
        int buttonsay = 0; // Button sayım değişkeni
        int [,] xYolustur = new int [2,2]; // Çift boyutlu dizi koordinatları tutması için
        public Form1() 
        {
            InitializeComponent();
        }
        private void Form1_MouseClick(object sender, MouseEventArgs e) // Form'a tıklandığında yapılacak işlemler
        {
            iptalbut.Click -= new EventHandler(iptalbut_Click); //İptal buttonun tıklanma sayısını eşit tutması için 
            onaylabut.Click -= new EventHandler(onaylabut_Click); // Onayla buttonun tıklanma sayısını eşit tutması için 
            Point point = this.PointToClient(Cursor.Position); // Mause'un tıklandığı yerde yeni Point(X,Y koordinat)
            obje.Location = new Point(point.X-10,point.Y-10); // Obje nesnesinin yeni koordinatı
            buttonsay++; // buttonsay +1 arttır
            if (buttonsay == 1) // Eğer button say 1 e eşitse 
            {
                xYolustur[0,0] = obje.Location.X+10; // Diziye tıklanan obje koordinatını at = X
                xYolustur[0,1] = obje.Location.Y+10; // Diziye tıklanan obje koordinatını at  = Y
                iptalbut.Name = "iptalbut"; //İptal buttonun oluşturma bilgisi
                iptalbut.Text = "İptal Et"; // İptal buttonun oluşturma bilgisi
                iptalbut.Location = new Point(point.X + 20, point.Y + 20); //İptal buttonun yeni koordinatları
                iptalbut.Click += new EventHandler(iptalbut_Click); // İptal buttonuna tıklanma özelliği ekleme
                iptalbut.Visible = true; //İptal buttonun görünürlüğünü aç
                onaylabut.Visible = false; //onayla buttonun görünürlüğünü kapat
                this.Controls.Add(iptalbut); //İptal buttonun Form'da kontrolüne izin ver 
                MessageBox.Show(String.Format("Başlangıç ucu \n'{0}' olarak belirlendi. \nBitiş ucunu seçin.",point),"Seçim İşlemi",MessageBoxButtons.OK,MessageBoxIcon.Asterisk); // Bilgilendirme mesajı
            }
            else if (buttonsay == 2) //Eğer Form'a 2 kere tıklandıysa
            {
                xYolustur[1,0] = obje.Location.X+10; //Bitiş noktasının diziye atılması : X
                xYolustur[1, 1] = obje.Location.Y+10; // Bitiş noktasının diziye atılması : Y
                onaylabut.Name = "onaylabut"; // onayla buttonun oluşturma bilgileri
                onaylabut.Text = "Onayla-Çiz"; // onayla buttonun oluşturma bilgileri 
                iptalbut.Click += new EventHandler(iptalbut_Click);//İptal buttonuna tıklanma özelliği ekleme 
                iptalbut.Location = new Point(point.X + 20, point.Y + 20); //iptal buttonun yeni koordinatları
                onaylabut.Location = new Point(point.X + 20, point.Y + 50); //Onayla buttonun yeni koordinatları
                buttonsay = 0; //Form'a tıklanma sayısını sıfırla
                onaylabut.Click+=new EventHandler(onaylabut_Click); //Onayla buttonuna tıklanma özelliği ekleme
                onaylabut.Visible = true; // Onayla buttonun görünürlüğünü aç
                this.Controls.Add(onaylabut); // Onayla buttonun Form'da kontrolünü aktifleştir
            }
        }
       void onaylabut_Click(object sender, EventArgs e) //Onayla buttonuna tıklandığında yapılacak işlemler
        {
                engelsay++; //Engel sayısı +1 arttırılsın (Bilgilendirme amaçlı)
                canvas.Parent = this; //ShapeContainer'ın formda kontrolüne izin ver (Çizim için) - »Kalem
                LineShape engel1 = new LineShape(); //Çizgi sınıfının oluşturulması - »Çizilecek nesne
                engel1.Name = "engel"; //Çizginin ismi 
                engel1.Parent = canvas; //Kalem ile neyi çizeceğimizi belirtmek
                engel1.StartPoint = new System.Drawing.Point(xYolustur[0, 0], xYolustur[0, 1]); //Oluşturulacak çizginin başlangıç koordinatları
                engel1.EndPoint = new System.Drawing.Point(xYolustur[1, 0], xYolustur[1, 1]); //Oluşturulacak çizginin bitiş koordinatları
                StreamWriter engellog = File.AppendText("engelkoordinat.txt");//.txt yazma sınıfı 
                engellog.WriteLine(String.Format("Engel No :{0} | Engel Başlangıç ; X:{1},Y:{2} | Engel Bitiş ; X:{3},Y:{4} | Engel Tarih {5}", engelsay, xYolustur[0, 0], xYolustur[0, 1], xYolustur[1, 0], xYolustur[1, 1], DateTime.Now)); //.txt yazım işlemi
                engellog.WriteLine("                                 -                       "); //.txt yazım işlemi
                engellog.Close(); //.txt yazma sınıfı bağlantı kapatıldı
            MessageBox.Show(String.Format("Engel başarıyla oluşturuldu !\n\nBilgiler ; \nBaşangıç noktası ; X : {0} , Y : {1}\nBitiş noktası ; X : {2}, Y : {3}\nToplam engel sayısı ; {4}\nKayıt yeri : {5} ",xYolustur[0,0],xYolustur[0,1],xYolustur[1,0],xYolustur[0,1],engelsay,Application.StartupPath),"Oluşturma Başarılı",MessageBoxButtons.OK,MessageBoxIcon.Asterisk);//bilgilendirme mesajı
            onaylabut.Visible = false; // Onayla button görünürlüğünü kapat 
            iptalbut.Visible = false; //İptal buttonun görünürlüğünü kapat
            buttonsay = 0; //Form'a tıklanma sayısını sıfırla
        }
        void iptalbut_Click(object sender, EventArgs e) //İptal buttonuna tıklandığında yapılacak işlemler
        {
            switch (buttonsay) //Forma'a tıklanma sayısına gelecek şartlar
            {
                case 1: //Eğer 1 ise 
                    xYolustur[0,0] = 0; //Başangıç X Koordinatları sıfırla 
                    xYolustur[0, 1] = 0; // Başlangıç Y koordinatları sıfırla 
                    onaylabut.Visible = false; //Onayla buttonunun görünürlüğünü kapat
                    buttonsay = 0; //Form'a tıklanma sayısını sıfırla 
                    MessageBox.Show("İptal edildi. (Aşama : 1)","İptal",MessageBoxButtons.OK,MessageBoxIcon.Warning); // Bilgilendirme mesajı
                    break; //Switch-Case den çık
                default : //Eğer 1 değilse 
                    xYolustur[0,0] = 0;  //Başlangıç X koordinatlarını sıfırla 
                    xYolustur[0, 1] = 0; //Başlangıç Y koordinatlarını sıfırla 
                    xYolustur[1, 0] = 0; // Bitiş X koordinatlarını sıfırla 
                    xYolustur[1, 1] = 0; // Bitiş Y koordinatlarını sıfırla 
                    buttonsay = 0; // Form'a tıklanma sayısını sfırıla 
                    MessageBox.Show("İptal edildi. (Aşama : 2)", "İptal", MessageBoxButtons.OK, MessageBoxIcon.Warning); // bilgilendirme mesajı
                    onaylabut.Visible = false; // Onayla buttonun görünürlüğünü kapat
                    break; // Switch - Case den çık
            }
        }
    }
}
