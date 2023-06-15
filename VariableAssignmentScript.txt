using System;
using System.Windows.Forms;
using Askom.AsixEvo.Scripting;

public class VariableAssignment : IScript
{
	IApplication application;
	IVariable[] asix6, asixEVO;
	IEventInfo EventInfo;

	public bool Initialize(string[] aParameters, IApplication aApplication) 
	{
		application = aApplication;
		asixEVO = new IVariable[]{
		AsixVarAssign("K5_A016_C", true),
AsixVarAssign("K9_Z016_I", true),
AsixVarAssign("K9_Z016_H", true),
AsixVarAssign("K9_Z016_L", true),
AsixVarAssign("K9_Z016_LL", true),
AsixVarAssign("K9_Z016_HH", true),
AsixVarAssign("K9_Z025_C", true),
AsixVarAssign("K9_Z025E_C", true),
AsixVarAssign("K9_Z025_H", true),

	}; //Above are examples that we replace with our variables.
		
		//-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
		//-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
		//-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
		
		application.ReportMessage(EvoMessageType.Info, "Table #1 initialized");
		asix6 = new IVariable[]{
			AsixVarAssign("K5_A016C", true),
AsixVarAssign("K9_Z016I", true),
AsixVarAssign("K9_Z016MAX", true),
AsixVarAssign("K9_Z016MIN", true),
AsixVarAssign("K9_Z016MNK", true),
AsixVarAssign("K9_Z016MXK", true),
AsixVarAssign("K9_Z025C", true),
AsixVarAssign("K9_Z025EC", true),
AsixVarAssign("K9_Z025MAX", true),


		}; //Above are examples that we replace with our variables.
		
		
		application.ReportMessage(EvoMessageType.Info, "Table #2 initialized");

		foreach (IVariable ivar in asix6)
		{
			if (ivar == null)
			{application.ReportMessage(EvoMessageType.Error, "Error during script initialization phase");
			return true;
		}
		}

		foreach (IVariable jvar in asixEVO)
		{
			if (jvar == null)
			{
				application.ReportMessage(EvoMessageType.Error, "Error during script initialization phase");
				return true;
					}
		}		
		
		EventInfo = application.RegisterTimeEvent(assignValue, TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(0)); //start every time and shift in phase
		application.ReportMessage(EvoMessageType.Info, "Running the script");
return false;
	}



	public void assignValue()
	{
		int i = 0;
		foreach (IVariable ivar in asix6)
		{
			if (ivar.VariableState.IsGood)
			{
				asixEVO[i].SetVariableValue(ivar.VariableState.AsDouble, Askom.Ascent.StatusOpc.Good);
			}
			else
			{
				asixEVO[i].SetVariableValue(ivar.VariableState.AsDouble, Askom.Ascent.StatusOpc.Bad);
			}

			i++;
		}
	}




	public IVariable AsixVarAssign(string aVarName, bool aRefresh)
	{
		IVariable var = application.GetVariable(aVarName, aRefresh);
		if (var == null)
		{
			application.ReportMessage(EvoMessageType.Error, "No variable" + aVarName);
		}

		return var;
	}

	public void FinalizeScript()
	{



		if (asix6 != null)
		{
			foreach (IVariable i in asix6)
				if (i != null)
					i.Dispose();
		}
		if (asixEVO != null)
		{
			foreach (IVariable i in asixEVO)
				if (i != null)
					i.Dispose();
		}
		
		application.ReportMessage(EvoMessageType.Info, "Closing the Variable Assignment script");
	}
}