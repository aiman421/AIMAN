using System;
using System.Threading.Tasks;
using System.Windows.Forms;
namespace DelegateExercise
{
    // The main class of the application, representing the form
    public partial class Form1 : Form
    {
        // Define a simple delegate that describes the structure of a method that updates the UI with a string message
        private delegate void UpdateUIDelegate(string message);
        // Define a simple event using the delegate. This event will be triggered when a simulated event occurs.
        private event UpdateUIDelegate SimpleEventOccurred;
        // A flag to indicate whether the event simulation is running or not
        private bool isRunning = false;
        // Constructor method for initializing the form
        public Form1()
        {
            InitializeComponent();
            InitializeEventHandlers();
        }
        // Method to set up event handlers, connecting the event to a method that updates the UI
        private void InitializeEventHandlers()
        {
            // Subscribe the OnSimpleEventOccurred method to the SimpleEventOccurred event
            SimpleEventOccurred += OnSimpleEventOccurred;
        }
        /// Method that gets called when a simulated event occurs, updating the UI with the event message
        private void OnSimpleEventOccurred(string message)
        {
            // Update the UI by adding the event message to a list with a timestamp
            UpdateUI(message);
        }
        // Method to update the UI with a given message
        private void UpdateUI(string message)
        {
            // Use Invoke to update UI controls in a thread-safe manner
            if (listBoxEvents.InvokeRequired)
            {
                // If we're not on the UI thread, invoke the method on the UI thread
                listBoxEvents.Invoke(new UpdateUIDelegate(UpdateUI), message);
            }
            else
            {
                // Insert the event message with a timestamp at the beginning of the ListBox
                listBoxEvents.Items.Insert(0, $"{DateTime.Now}: {message}");
            }
        }
        // Method to simulate simple events asynchronously without freezing the UI
        private async void SimulateSimpleEvents()
        {
            // Create a random number generator for selecting different event types
            Random random = new Random();
            // Simulate events while the isRunning flag is true
            while (isRunning)
            {
                // Generate a random event type (0, 1, or 2)
                int eventType = random.Next(3);
                // Trigger the event with an appropriate message based on the event type
                switch (eventType)
                {
                    // Invoke the SimpleEventOccurred event with a specific message based on the event type
                    case 0:
                        SimpleEventOccurred?.Invoke("Standby event");
                        break;
                    case 1:
                        SimpleEventOccurred?.Invoke("Scheduled time event");
                        break;
                    case 2:
                        SimpleEventOccurred?.Invoke("Non-scheduled time event");
                        break;
                }
                // Simulate some delay between events
                await Task.Delay(2000);
            }
        }
        // Method to handle the button click event and start simulating events
        private void btnStart_Click(object sender, EventArgs e)
        {
            // Start simulating simple events asynchronously
            isRunning = true;
            Task.Run(() => SimulateSimpleEvents());
            btnStart.Enabled = false;
            btnStop.Enabled = true;
        }

        // Method to handle the button click event and stop the event simulatio
        private void btnStop_Click(object sender, EventArgs e)
        {
            // Stop the event simulation
            isRunning = false;
            btnStart.Enabled = true;
            btnStop.Enabled = false;
        }
    }
}

    
