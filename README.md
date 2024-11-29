# tersPerspektif

private void button1_Click(object sender, EventArgs e)
{
    Graphics g = Graphics.FromImage(newBitmap);
    g.Clear(SystemColors.Control);

    xa = Convert.ToInt32(textBox1.Text);
    ya = Convert.ToInt32(textBox2.Text);
    za = Convert.ToInt32(textBox3.Text);
    alfa = Convert.ToInt32(textBox4.Text);
    beta = Convert.ToInt32(textBox5.Text);
    teta = Convert.ToInt32(textBox6.Text);
    D = Convert.ToInt32(textBox7.Text);

    ttransformasyon(alfa, beta, teta);

    int x_m, z_m, i, j;
    double fonk_1, fonk_2, fonk_3, fonk_4, mod1, mod2;
    for (i = 0; i < 400; i++)
    {
        for (j = 0; j < 400; j++)
        {
            x_m = j - 200; //x ve z noktalarÄ± merkeze taÅŸÄ±nÄ±yor.
            z_m = -i + 200;
            K = (-za / payda);
            X = xa + K * (Rt[0, 0] * x_m + D * Rt[0, 1] + z_m * Rt[0, 2]); //K deÄŸerine gÃ¶re X ve Y'nin yeni deÄŸerleri atanÄ±yor.
            Y = ya + K * (Rt[1, 0] * x_m + D * Rt[1, 1] + z_m * Rt[1, 2]);
            payda = Rt[2, 0] * x_m + D * Rt[2, 1] + Rt[2, 2] * z_m;
            if (payda == 0) //Payda eÄŸer 0 olursa K deÄŸerimiz de payda 0 olamayacaÄŸÄ± iÃ§in sonsuz Ã§Ä±kar bu istenmedik bir durum
                continue;

            if (K > 0)
            {
                fonk_1 = Math.Floor(X + 0.5);//Floor fonksiyonu ile sayÄ±lar yakÄ±n olan tam sayÄ± deÄŸerine yuvarlanÄ±yor.
                fonk_2 = Math.Floor(Y + 0.5);
                fonk_3 = texture.Width;
                fonk_4 = texture.Height;
                mod1 = Math.Abs(fonk_1 % fonk_3); //Mod iÅŸlemi ile dokuyu doldur.
                mod2 = Math.Abs(fonk_2 % fonk_4);

                if (Y < 0) //(X,Y)=(0,0) dan Ã¶ncesine geÃ§iÅŸimiz olursa gÃ¶rÃ¼ntÃ¼yÃ¼ doÄŸru ÅŸekilde tutmak iÃ§in ters alma iÅŸlemi uygularÄ±z.
                    mod2 = (int)(texture.Height - mod2) % texture.Height;

                if (X < 0)
                    mod1 = texture.Width - mod1 - 1;
                newBitmap.SetPixel(j, i, texture.GetPixel(Convert.ToInt32(mod1), Convert.ToInt32(mod2))); //piksellerle oluÅŸturulan resimi belleÄŸe at,resmi oluÅŸtur.
            }
        }
    }
    // Resimi programdaki image box kÄ±smÄ±ndaki yere bastÄ±r.
    pictureBox1.Image = newBitmap;

}

----------------------------------------------------------------------------------------------
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace TersPerspektif
{

    public partial class Form1 : Form
    {
            double[,] R;
            double[,] Rt;
            int xa, ya, za, nxs, nzs;
            double alfa, beta, teta, K, X, Y, bX, bY, payda;
           // String filename;
            double D;
            Bitmap texture;
            Bitmap newBitmap = new Bitmap(400,400);

        private void pictureBox1_Click(object sender, EventArgs e)
        {
            
        }


        double[] sinus;
            double[] cosinus;
        public Form1()
        {
            InitializeComponent();
            R = new double[3, 3];  //transformasyon matrisi
            Rt = new double[3, 3];  //ters transformasyon matrisi
            sinus = new double[1800];
            cosinus=new double[1800];
            for (int i = 0; i < 1800; i++)
            {
                sinus[i] = Math.Sin(i * Math.PI / 1800);
                cosinus[i] = Math.Cos(i * Math.PI / 1800);
            }

        }

        public void ttransformasyon(double alfa, double beta, double teta)
        {
            Rt[0, 0] = Math.Cos(beta * Math.PI / 180) * Math.Cos(teta * Math.PI / 180);
            Rt[0, 1] = Math.Cos(beta * Math.PI / 180) * Math.Sin(teta * Math.PI / 180);
            Rt[0, 2] = - Math.Sin(beta * Math.PI / 180);

            Rt[1, 0] = - Math.Cos(alfa * Math.PI / 180) * Math.Sin(teta * Math.PI / 180) + Math.Sin(beta * Math.PI / 180) * Math.Sin(alfa * Math.PI / 180) * Math.Cos(teta * 3.14 / 180);
            Rt[1, 1] = Math.Cos(alfa * Math.PI / 180) * Math.Cos(teta * Math.PI / 180) + Math.Sin(beta * Math.PI / 180) * Math.Sin(alfa * Math.PI / 180) * Math.Sin(teta * Math.PI / 180);;
            Rt[1, 2] = Math.Cos(beta * Math.PI / 180) * Math.Sin(alfa * Math.PI / 180);

            Rt[2, 0] = Math.Sin(teta * Math.PI / 180) * Math.Sin(alfa * Math.PI / 180)  + Math.Sin(beta * Math.PI / 180) * Math.Cos(alfa * Math.PI / 180) * Math.Cos(teta * 3.14 / 180);
            Rt[2, 1] = - Math.Sin(alfa * Math.PI / 180) * Math.Cos(teta * Math.PI / 180) + Math.Sin(beta * Math.PI / 180) * Math.Cos(alfa * Math.PI / 180) * Math.Sin(teta * Math.PI / 180);
            Rt[2, 2] = Math.Cos(beta * Math.PI / 180) * Math.Cos(alfa * Math.PI / 180);
        }

        public void transformasyon(double alfa, double beta, double teta)
        {
            R[0, 0] = Math.Cos(beta * Math.PI / 180) * Math.Cos(teta * Math.PI / 180);
            R[0, 1] = - Math.Cos(alfa * Math.PI / 180) * Math.Sin(teta * Math.PI / 180) + Math.Sin(beta * Math.PI / 180) * Math.Sin(alfa * Math.PI / 180) * Math.Cos(teta * 3.14 / 180);
            R[0, 2] = Math.Sin(teta * Math.PI / 180) * Math.Sin(alfa * Math.PI / 180)  + Math.Sin(beta * Math.PI / 180) * Math.Cos(alfa * Math.PI / 180) * Math.Cos(teta * 3.14 / 180);

            R[1, 0] = Math.Cos(beta * Math.PI / 180) * Math.Sin(teta * Math.PI / 180);
            R[1, 1] = Math.Cos(alfa * Math.PI / 180) * Math.Cos(teta * Math.PI / 180) + Math.Sin(beta * Math.PI / 180) * Math.Sin(alfa * Math.PI / 180) * Math.Sin(teta * Math.PI / 180);;
            R[1, 2] = - Math.Sin(alfa * Math.PI / 180) * Math.Cos(teta * Math.PI / 180) + Math.Sin(beta * Math.PI / 180) * Math.Cos(alfa * Math.PI / 180) * Math.Sin(teta * Math.PI / 180);

            R[2, 0] = - Math.Sin(beta * Math.PI / 180);
            R[2, 1] = Math.Cos(beta * Math.PI / 180) * Math.Sin(alfa * Math.PI / 180);
            R[2, 2] = Math.Cos(beta * Math.PI / 180) * Math.Cos(alfa * Math.PI / 180);
        }



        //------------------------------------------------------------------------------------------------------
        //Bu fonksiyon girilen parametrelere göre 400x400'lük bir alana ters perspektif dönüşüm ile düzlemsel yüzeye doku kaplar.
        //Default parametreler (X,Y,Z)=(0,60,150) (alfa,beta,teta)=(0,0,0)
        private void button1_Click(object sender, EventArgs e)
        {
            Graphics g = Graphics.FromImage(newBitmap);
            g.Clear(SystemColors.Control);

            xa = Convert.ToInt32(textBox1.Text);
            ya = Convert.ToInt32(textBox2.Text);
            za = Convert.ToInt32(textBox3.Text);
            alfa = Convert.ToInt32(textBox4.Text);
            beta = Convert.ToInt32(textBox5.Text);
            teta = Convert.ToInt32(textBox6.Text);
            D = Convert.ToInt32(textBox7.Text);

            ttransformasyon(alfa, beta, teta);

            int x_m, z_m, i, j;
            double fonk_1, fonk_2, fonk_3, fonk_4, mod1, mod2;
            for (i = 0; i < 400; i++)
            {
                for (j = 0; j < 400; j++)
                {
                    x_m = j - 200; //x ve z noktalarÄ± merkeze taÅŸÄ±nÄ±yor.
                    z_m = -i + 200;
                    K = (-za / payda);
                    X = xa + K * (Rt[0, 0] * x_m + D * Rt[0, 1] + z_m * Rt[0, 2]); //K deÄŸerine gÃ¶re X ve Y'nin yeni deÄŸerleri atanÄ±yor.
                    Y = ya + K * (Rt[1, 0] * x_m + D * Rt[1, 1] + z_m * Rt[1, 2]);
                    payda = Rt[2, 0] * x_m + D * Rt[2, 1] + Rt[2, 2] * z_m;
                    if (payda == 0) //Payda eÄŸer 0 olursa K deÄŸerimiz de payda 0 olamayacaÄŸÄ± iÃ§in sonsuz Ã§Ä±kar bu istenmedik bir durum
                        continue;

                    if (K > 0)
                    {
                        fonk_1 = Math.Floor(X + 0.5);//Floor fonksiyonu ile sayÄ±lar yakÄ±n olan tam sayÄ± deÄŸerine yuvarlanÄ±yor.
                        fonk_2 = Math.Floor(Y + 0.5);
                        fonk_3 = texture.Width;
                        fonk_4 = texture.Height;
                        mod1 = Math.Abs(fonk_1 % fonk_3); //Mod iÅŸlemi ile dokuyu doldur.
                        mod2 = Math.Abs(fonk_2 % fonk_4);

                        if (Y < 0) //(X,Y)=(0,0) dan Ã¶ncesine geÃ§iÅŸimiz olursa gÃ¶rÃ¼ntÃ¼yÃ¼ doÄŸru ÅŸekilde tutmak iÃ§in ters alma iÅŸlemi uygularÄ±z.
                            mod2 = (int)(texture.Height - mod2) % texture.Height;

                        if (X < 0)
                            mod1 = texture.Width - mod1 - 1;
                        newBitmap.SetPixel(j, i, texture.GetPixel(Convert.ToInt32(mod1), Convert.ToInt32(mod2))); //piksellerle oluÅŸturulan resimi belleÄŸe at,resmi oluÅŸtur.
                    }
                }
            }
            // Resimi programdaki image box kÄ±smÄ±ndaki yere bastÄ±r.
            pictureBox1.Image = newBitmap;

        }

        //---------------------------------------------------------------------------
        //Bu fonksiyon kullanıcıdan resim seçmesini ister ve belleğe yükler.
        private void button2_Click(object sender, EventArgs e)
        {
            resimAc.Title = "Browse BMP Files";
            resimAc.DefaultExt = "bmp";
            resimAc.Filter = "BMP files (*.bmp)|*.BMP";
            if (resimAc.ShowDialog() == DialogResult.OK)
            {
                texture = new Bitmap(resimAc.FileName);
            }
        }


//---------------------------------------------------------------------------
//Ters perspektif dönüşüm mantığı ile silindir yüzeyine doku kaplamayı gerçekleştiren fonksiyon.
//Default parametreler (X,Y,Z)=(0,300,150) (alfa,beta,teta)=(0,0,0)
private void button3_Click(object sender, EventArgs e)
{
    Graphics g = Graphics.FromImage(newBitmap);
    g.Clear(SystemColors.Control);

    xa = Convert.ToInt32(textBox1.Text);
    ya = Convert.ToInt32(textBox2.Text);
    za = Convert.ToInt32(textBox3.Text);
    alfa = Convert.ToInt32(textBox4.Text);
    beta = Convert.ToInt32(textBox5.Text);
    teta = Convert.ToInt32(textBox6.Text);
    D = Convert.ToInt32(textBox7.Text);

    if (texture == null)
    {
        MessageBox.Show("Lütfen bir doku resmi seçin!");
        return;
    }

    ttransformasyon(alfa, beta, teta);

    // Silindir için doku kaplama
    double radius = 150;
    double height = 300;

    for (int y = -150; y < 150; y++) // Silindirin yüksekliği boyunca
    {
        for (double theta = 0; theta < 360; theta += 1) // 360 derece boyunca döndür
        {
            double x = radius * Math.Cos(theta * Math.PI / 180);
            double z = radius * Math.Sin(theta * Math.PI / 180);

            // Ters perspektif dönüşümü
            X = Rt[0, 0] * x + Rt[0, 1] * y + Rt[0, 2] * z + xa;
            Y = Rt[1, 0] * x + Rt[1, 1] * y + Rt[1, 2] * z + ya;
            payda = Rt[2, 0] * x + Rt[2, 1] * y + Rt[2, 2] * z + za + D;

            if (payda != 0)
            {
                bX = (X * D) / payda + 200;
                bY = (Y * D) / payda + 200;

                if (bX >= 0 && bX < 400 && bY >= 0 && bY < 400)
                {
                    Color color = texture.GetPixel((int)((theta / 360) * texture.Width), (int)((y + 150) / 300 * texture.Height));
                    newBitmap.SetPixel((int)bX, (int)bY, color);
                }
            }
        }
    }

    pictureBox1.Image = newBitmap;
}

        //---------------------------------------------------------------------------
        // Ortografik dönüşüm ile yarım küre üzerine doku kaplama fonksiyonu.
        // Default parametreler (X,Y,Z)=(0,0,0) (alfa,beta,gama)=(0,0,180)
        private void button4_Click(object sender, EventArgs e)
        {
            // Grafik oluşturuluyor
            Graphics g = Graphics.FromImage(newBitmap);
            g.Clear(SystemColors.Control);

            // Eğer texture seçilmemişse hata mesajı göster
            if (texture == null)
            {
                MessageBox.Show("Lütfen bir doku resmi seçin!");
                return;
            }

            // Resim boyutlarını kontrol et
            if (texture.Width == 0 || texture.Height == 0)
            {
                MessageBox.Show("Geçersiz resim dosyası!");
                return;
            }

            // Küre boyutları
            double radius = 150; // Yarım kürenin yarıçapı

            // Yarım küre için doku kaplama işlemi
            for (double phi = 0; phi <= 180; phi += 1) // Enlem açısı (0° - 180°)
            {
                for (double theta = 0; theta < 360; theta += 1) // Boylam açısı (0° - 360°)
                {
                    // Kürenin yüzeyindeki 3D koordinatları hesapla
                    double x = radius * Math.Sin(phi * Math.PI / 180) * Math.Cos(theta * Math.PI / 180);
                    double y = radius * Math.Sin(phi * Math.PI / 180) * Math.Sin(theta * Math.PI / 180);
                    double z = radius * Math.Cos(phi * Math.PI / 180);

                    // Ortografik dönüşüm: 3D koordinatları 2D düzleme yansıt
                    double screenX = x + 200; // X ekseni düzeltme (merkezleme)
                    double screenY = z + 200; // Y ekseni düzeltme (merkezleme)

                    // Dokuyu yansıtma işlemi
                    int textureX = (int)((theta / 360) * texture.Width); // Dokudaki X koordinatı
                    int textureY = (int)((phi / 180) * texture.Height);  // Dokudaki Y koordinatı

                    if (textureX >= 0 && textureX < texture.Width && textureY >= 0 && textureY < texture.Height)
                    {
                        Color color = texture.GetPixel(textureX, textureY); // Dokudan renk al
                        if (screenX >= 0 && screenX < 400 && screenY >= 0 && screenY < 400)
                        {
                            newBitmap.SetPixel((int)screenX, (int)screenY, color); // Ekrana yaz
                        }
                    }
                }
            }

            // Yarım küre görüntüsünü PictureBox'ta göster
            pictureBox1.Image = newBitmap;
        }
        //---------------------------------------------------------------------------
        //Bu fonksiyon girilen parametrelere göre açısal tarama mantığına dayalı ters perspektif dönüşüm ile doku kaplar.
        //Default parametreler (X,Y,Z)=(0,60,150) (alfa,beta,teta)=(0,0,0)
        private void button5_Click(object sender, EventArgs e)
        {
        }

//---------------------------------------------------------------------------
//Perspektif dönüşüm kullanarak silindir yüzeye doku kaplamayı gerçekleştirir.
//Default parametreler (X,Y,Z)=(0,300,150) (alfa,beta,teta)=(0,0,0)
        private void button6_Click(object sender, EventArgs e)
        {
        }

//---------------------------------------------------------------------------
//Anti-alising uygulayarak z=0 yüzeyine ters perspektif dönüşüm ile doku kaplayan fonksiyon.
//Default parametreler (X,Y,Z)=(0,60,150) (alfa,beta,teta)=(0,0,0)
        private void button7_Click(object sender, EventArgs e)
        {
        }

//---------------------------------------------------------------------------
// Ortografik dönüşüm ile oluşturulan yarım küre üzerindeki belirli bir küre dilimine doku germe uygulayan fonksiyon.
// Default parametreler (X,Y,Z)=(0,0,0) (alfa,beta,gama)=(0,0,180)
        private void button8_Click(object sender, EventArgs e)
        {
        }
    }
}
