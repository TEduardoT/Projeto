using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
using Microsoft.Speech.Recognition;
using System.IO.Ports;

namespace Robotica
{
    
    public partial class Central : Form
    {

        

        SerialPort connection;

        private SpeechRecognitionEngine engine;
        
        public bool b_button = false;
       

        public Central()
        {
            InitializeComponent();
        }

        private void Central_Load_1(object sender, EventArgs e)
        {
            
            Load_Reconhecimento();
        }
        private void Load_Reconhecimento()
        {
            try
            {
                

               
                
                engine = new SpeechRecognitionEngine(new System.Globalization.CultureInfo("pt-BR"));//definir cultura
                engine.SetInputToDefaultAudioDevice();
                engine.EndSilenceTimeoutAmbiguous = TimeSpan.FromSeconds(0.1); // Determine o tempo pausa de reconhecimento 
                
                //****************Gramatica**********************/
                string[] lampada = { "pisque","Ligue Lâmpada","Acende Luz","Pisque","Desliga Lâmpada","ligue","apague", "Desliga luz","Apague luz",};
                //*************************************************//
               
                //***************Carregamento da Gramatica********************//
                engine.LoadGrammar(new Grammar(new GrammarBuilder(new Choices(lampada))));
                
                //========//
                
                engine.SpeechRecognized += new EventHandler<SpeechRecognizedEventArgs>(rec);
                engine.RecognizeAsync(RecognizeMode.Multiple);
            }
            #region Catch
            catch (Exception ex)
            { MessageBox.Show("Ocorreu erro " + ex); }
            #endregion
        }

        private void rec(Object s,SpeechRecognizedEventArgs e)
        {
            ////////////////Se o botão estiver como ligado//////////////////

            if (select.Text != "") {
                connection = new SerialPort(select.Text, 9600);
                if (b_button == true)
                {
                   
                    if (e.Result.Confidence > 0.10)
                    {
                        string fala = e.Result.Text;
                        // MessageBox.Show(fala);
                        if (fala == "Ligue Lâmpada" || fala == "Acende Luz" || fala == "ligue")
                        {
                            connection.Open();
                            connection.Write("1");
                            connection.Close();
                        }
                        if (fala == "Desliga Lâmpada" || fala == "Desliga luz" || fala == "Apague luz" || fala == "apague")
                        {
                            connection.Open();
                            connection.Write("0");
                            connection.Close();
                        }

                    }

                }
            }
            
     }



        private void button1_Click(object sender, EventArgs e)
        {
            if (select.Text != "")
            {
                if (button1.Text == "Ligar")
                {

                    b_button = true;
                    button1.Text = "Desligar";
                }
                 else if (button1.Text == "Desligar")
            {
                b_button = false;
                button1.Text = "Ligar";
            }
            }
            else{MessageBox.Show("Selecione a Porta de conexão");}
           
        }

        private void button3_Click(object sender, EventArgs e)
        {
            if (select.Text != "")
            {
                connection = new SerialPort(select.Text, 9600);
                connection.Open();
                connection.Write("1");
                connection.Close();
            }

        }

        private void button2_Click(object sender, EventArgs e)
        {
            if (select.Text != "")
            {
                connection = new SerialPort(select.Text, 9600);
                connection.Open();
                connection.Write("0");
                connection.Close();
            }
        }


       
    }

}
