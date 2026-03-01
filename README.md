using System;
using System.IO;
using System.Linq;
using System.Windows.Forms;

namespace DigitProductParity
{
    public partial class MainForm : Form
    {
        private TextBox txtInputFile;
        private TextBox txtOriginalNumber;
        private Label lblResult;
        private Button btnOpenFile;
        private Button btnCheck;
        private Button btnSaveResult;
        private string originalNumber;
        private bool isProductEven;

        public MainForm()
        {
            InitializeComponent();
        }

        private void InitializeComponent()
        {
            this.Text = "анализ чётности произведения цифр";
            this.Size = new System.Drawing.Size(500, 300);

            txtInputFile = new TextBox { Location = new System.Drawing.Point(10, 10), Width = 300, ReadOnly = true };
            btnOpenFile = new Button { Text = "открыть файл", Location = new System.Drawing.Point(320, 10), Width = 150 };
            btnOpenFile.Click += BtnOpenFile_Click;

            Label lblOriginal = new Label { Text = "исходное число:", Location = new System.Drawing.Point(10, 50), AutoSize = true };
            txtOriginalNumber = new TextBox { Location = new System.Drawing.Point(10, 70), Width = 460, ReadOnly = true, Multiline = true, Height = 60 };

            btnCheck = new Button { Text = "проверить чётность", Location = new System.Drawing.Point(10, 140), Width = 150 };
            btnCheck.Click += BtnCheck_Click;

            Label lblRes = new Label { Text = "результат:", Location = new System.Drawing.Point(10, 180), AutoSize = true };
            lblResult = new Label { Text = "", Location = new System.Drawing.Point(80, 180), AutoSize = true, Font = new System.Drawing.Font("Arial", 10, System.Drawing.FontStyle.Bold) };

            btnSaveResult = new Button { Text = "сохранить результат", Location = new System.Drawing.Point(10, 220), Width = 150, Enabled = false };
            btnSaveResult.Click += BtnSaveResult_Click;

            this.Controls.Add(txtInputFile);
            this.Controls.Add(btnOpenFile);
            this.Controls.Add(lblOriginal);
            this.Controls.Add(txtOriginalNumber);
            this.Controls.Add(btnCheck);
            this.Controls.Add(lblRes);
            this.Controls.Add(lblResult);
            this.Controls.Add(btnSaveResult);
        }

        private void BtnOpenFile_Click(object sender, EventArgs e)
        {
            using (OpenFileDialog openFileDialog = new OpenFileDialog())
            {
                openFileDialog.Filter = "текстовые файлы (*.txt)|*.txt|все файлы (*.*)|*.*";
                if (openFileDialog.ShowDialog() == DialogResult.OK)
                {
                    try
                    {
                        string content = File.ReadAllText(openFileDialog.FileName);
                        string digitsOnly = new string(content.Where(char.IsDigit).ToArray());

                        if (string.IsNullOrEmpty(digitsOnly))
                        {
                            MessageBox.Show("файл не содержит цифр.", "ошибка", MessageBoxButtons.OK, MessageBoxIcon.Error);
                            return;
                        }

                        if (digitsOnly.Length > 100)
                        {
                            MessageBox.Show("число содержит более 100 цифр.", "ошибка", MessageBoxButtons.OK, MessageBoxIcon.Error);
                            return;
                        }

                        originalNumber = digitsOnly;
                        txtInputFile.Text = openFileDialog.FileName;
                        txtOriginalNumber.Text = originalNumber;
                        lblResult.Text = "";
                        btnSaveResult.Enabled = false;
                    }
                    catch (Exception ex)
                    {
                        MessageBox.Show($"ошибка при чтении файла: {ex.Message}", "ошибка", MessageBoxButtons.OK, MessageBoxIcon.Error);
                    }
                }
            }
        }

        private void BtnCheck_Click(object sender, EventArgs e)
        {
            if (string.IsNullOrEmpty(originalNumber))
            {
                MessageBox.Show("сначала откройте файл с числом.", "предупреждение", MessageBoxButtons.OK, MessageBoxIcon.Warning);
                return;
            }

            isProductEven = originalNumber.Any(ch => (ch - '0') % 2 == 0);
            string resultText = isProductEven ? "чётное" : "нечётное";
            lblResult.Text = resultText;
            btnSaveResult.Enabled = true;
        }

        private void BtnSaveResult_Click(object sender, EventArgs e)
        {
            using (SaveFileDialog saveFileDialog = new SaveFileDialog())
            {
                saveFileDialog.Filter = "текстовые файлы (*.txt)|*.txt|все файлы (*.*)|*.*";
                saveFileDialog.FileName = "result.txt";
                if (saveFileDialog.ShowDialog() == DialogResult.OK)
                {
                    try
                    {
                        string output = $"исходное число: {originalNumber}\nпроизведение цифр: {(isProductEven ? "чётное" : "нечётное")}";
                        File.WriteAllText(saveFileDialog.FileName, output);
                        MessageBox.Show("результат сохранён.", "успех", MessageBoxButtons.OK, MessageBoxIcon.Information);
                    }
                    catch (Exception ex)
                    {
                        MessageBox.Show($"ошибка при сохранении: {ex.Message}", "ошибка", MessageBoxButtons.OK, MessageBoxIcon.Error);
                    }
                }
            }
        }
    }

    static class Program
    {
        [STAThread]
        static void Main()
        {
            Application.EnableVisualStyles();
            Application.SetCompatibleTextRenderingDefault(false);
            Application.Run(new MainForm());
        }
    }
}
```
