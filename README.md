
import React, { useState, useEffect } from 'react';
import axios from 'axios';

const CRMApp = () => {
  const [leads, setLeads] = useState([]);

  // Fetch leads on load
  useEffect(() => {
    const fetchLeads = async () => {
      const res = await axios.get('http://localhost:5000/api/leads');
      setLeads(res.data);
    };
    fetchLeads();
  }, []);

  const handleUpdate = async (id, field, value) => {
    await axios.patch(`http://localhost:5000/api/leads/${id}`, { [field]: value });
    // Refresh local state
    setLeads(leads.map(l => l._id === id ? { ...l, [field]: value } : l));
  };

  return (
    <div className="p-8 bg-gray-100 min-h-screen font-sans">
      <h1 className="text-3xl font-bold mb-6">Client Lead Manager</h1>
      
      <div className="bg-white shadow rounded-lg overflow-hidden">
        <table className="w-full text-left border-collapse">
          <thead className="bg-gray-200">
            <tr>
              <th className="p-4">Name</th>
              <th className="p-4">Email</th>
              <th className="p-4">Status</th>
              <th className="p-4">Notes</th>
            </tr>
          </thead>
          <tbody>
            {leads.map(lead => (
              <tr key={lead._id} className="border-b hover:bg-gray-50">
                <td className="p-4 font-medium">{lead.name}</td>
                <td className="p-4 text-gray-600">{lead.email}</td>
                <td className="p-4">
                  <select 
                    value={lead.status}
                    onChange={(e) => handleUpdate(lead._id, 'status', e.target.value)}
                    className="border rounded p-1"
                  >
                    <option value="New">New</option>
                    <option value="Contacted">Contacted</option>
                    <option value="Converted">Converted</option>
                  </select>
                </td>
                <td className="p-4">
                  <input 
                    type="text"
                    defaultValue={lead.notes}
                    onBlur={(e) => handleUpdate(lead._id, 'notes', e.target.value)}
                    placeholder="Click to add notes..."
                    className="w-full bg-transparent border-none outline-none focus:ring-1 focus:ring-blue-300 rounded"
                  />
                </td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>
    </div>
  );
};

export default CRMApp;
