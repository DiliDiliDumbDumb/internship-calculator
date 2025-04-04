# internship-calculator
import React, { useState } from "react";
import { addDays } from "date-fns";

const holidays = [
  "2025-01-01", "2025-04-09", "2025-04-10", "2025-05-01",
  "2025-06-12", "2025-08-21", "2025-11-01", "2025-12-25"
];

const calculateCompletionDate = (startDate, requiredHours, absences) => {
  if (!startDate || isNaN(requiredHours) || isNaN(absences)) return "";

  let currentDate = new Date(startDate);
  let remainingHours = requiredHours + absences * 8;

  while (remainingHours > 0) {
    currentDate = addDays(currentDate, 1);
    let dayOfWeek = currentDate.getDay();
    let formattedDate = currentDate.toISOString().split("T")[0];

    if (!holidays.includes(formattedDate)) {
      if (dayOfWeek >= 1 && dayOfWeek <= 5) {
        remainingHours -= 8; // Regular weekdays (8 hours)
        if (remainingHours > 0) remainingHours -= 2; // Possible weekday OT (2 hours)
      } else if (dayOfWeek === 6) {
        remainingHours -= 8; // One day of weekend OT (8 hours)
      }
    }
  }

  return currentDate.toDateString();
};

export default function App() {
  const [startDate, setStartDate] = useState("");
  const [requiredHours, setRequiredHours] = useState("");
  const [absences, setAbsences] = useState(0);
  const [completionDate, setCompletionDate] = useState("");

  const handleCalculate = () => {
    const result = calculateCompletionDate(startDate, parseInt(requiredHours), parseInt(absences));
    setCompletionDate(result);
  };

  return (
    <div style={{ maxWidth: "400px", margin: "auto", padding: "20px", textAlign: "center", fontFamily: "Arial" }}>
      <h2>Internship Completion Calculator</h2>
      
      <label>Start Date</label>
      <input type="date" value={startDate} onChange={(e) => setStartDate(e.target.value)}
        style={{ display: "block", width: "100%", padding: "8px", margin: "5px 0" }} />

      <label>Required Internship Hours</label>
      <input type="number" value={requiredHours} onChange={(e) => setRequiredHours(e.target.value)}
        style={{ display: "block", width: "100%", padding: "8px", margin: "5px 0" }} />

      <label>Number of Absences</label>
      <input type="number" value={absences} onChange={(e) => setAbsences(e.target.value)}
        style={{ display: "block", width: "100%", padding: "8px", margin: "5px 0" }} />

      <button onClick={handleCalculate}
        style={{ padding: "10px", marginTop: "10px", width: "100%", backgroundColor: "#007bff", color: "white", border: "none", cursor: "pointer" }}>
        Calculate Completion Date
      </button>

      {completionDate && <p style={{ marginTop: "20px", fontSize: "18px", fontWeight: "bold" }}>Expected Completion Date: {completionDate}</p>}
    </div>
  );
}
