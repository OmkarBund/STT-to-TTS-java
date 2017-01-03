# STT-to-TTS-java
this is my first project . using sphnix4 and freeTTS api for converting speech to text and then text to speech using java






every thing from lib files to dll files and from config files to java code file
working on windows 7 64 bit
////////////////////////////////////////////////////////////////////////////////////////////Rquirements ////////////////////////////////////////////////////////////////////////////////////////////
file requires the following libraries:

1. FreeTTS
	- cmu_time_awb.jar
	- cmu_us_kal.jar
	- cmudict04.jar
	- cmulex.jar
	- cmultimelex.jar
	- en_us.jar
	- freetts.jar
	- freetts-jsapi10.jar
	- jsapi.jar
	- mbrola.jar
2. Sphinx4
	- cgjsapi.jar
	- jsapi.jar
	- sphinx4.jar
	- WSJ_8gau_13dCep_16k_40mel_130Hz_6800Hz.jar
	- cgjsapi170_x64.dll
	- cgjsapi170.dll
////////////////////////////////////////////////////////////////////////////////////////////hello.gram file////////////////////////////////////////////////////////////////////////////////////////////
#JSGF V1.0;

/**
 * JSGF Grammar for Hello World example
 */

grammar hello;

public <greet> = (Hello |Hi| Welcome | Master |  Miss |Control | Task |Manager | Security);

////////////////////////////////////////////////////////////////////////////////////////////TSStoSTT.java file////////////////////////////////////////////////////////////////////////////////////////////
//Text to Speech lib files
import java.util.Locale;
import javax.speech.Central;
import javax.speech.synthesis.Synthesizer;
import javax.speech.synthesis.SynthesizerModeDesc;
//Speech to Text lib files
import edu.cmu.sphinx.frontend.util.Microphone;
import edu.cmu.sphinx.recognizer.Recognizer;
import edu.cmu.sphinx.result.Result;
import edu.cmu.sphinx.util.props.ConfigurationManager;
import edu.cmu.sphinx.util.props.PropertyException;
import java.io.File;
import java.io.IOException;
import java.net.URL;
import javax.speech.recognition.ResultToken;
public class TSStoSTT 
{
	public String speechtoText(URL url)
	{
		String nullValue = "";
		String resultText = "";
		try 
		{
			System.out.println("Loading...SpeechtoText");
			ConfigurationManager cm = new ConfigurationManager(url);
			Recognizer recognizer = (Recognizer) cm.lookup("recognizer");
			Microphone microphone = (Microphone) cm.lookup("microphone");
			recognizer.allocate();
			if (microphone.startRecording())
			{
				while (true)
				{
					System.out.println("Start speaking. Press Ctrl-C to quit.\n");
					Result result = recognizer.recognize();
					if (result != null)
					{
						System.out.println("Enter your choise"+ "\n");
						resultText = result.getBestFinalResultNoFiller();
						System.out.println("You said: " + resultText + "\n");
						return resultText;
					}
					else
					{
						System.out.println("I can't hear what you said.\n");
					}
				}
			}
			else
			{
				System.out.println("Cannot start microphone.");
				recognizer.deallocate();
				System.exit(1);
			}
		}
		/*
		catch (PropertyException e) 
		{
			System.err.println("Problem configuring HelloWorld: " + e);
			e.printStackTrace();
			return nullValue;
		}
		*/
		catch (Exception e) 
		{
			System.err.println("Problem in HelloWorld: " + e);
			e.printStackTrace();
			return nullValue;
		}
		return nullValue;
	}
	public void texttoSpeech(String str)
	{
		try
		{
			System.out.println("Loading...TexttoSpeech");
			System.setProperty("freetts.voices","com.sun.speech.freetts.en.us.cmu_us_kal.KevinVoiceDirectory");
			Central.registerEngineCentral("com.sun.speech.freetts.jsapi.FreeTTSEngineCentral");
			Synthesizer  synthesizer = Central.createSynthesizer(new SynthesizerModeDesc(Locale.US));
			synthesizer.allocate();
			synthesizer.resume();
			synthesizer.speakPlainText(str, null);
			synthesizer.waitEngineState(Synthesizer.QUEUE_EMPTY);
			synthesizer.deallocate();
		}
		/*
		catch(EngineException e)
		{
			System.err.println("Problem in HelloWorld: " + e);
			e.printStackTrace();
		}
		catch(InterruptedException e)
		{
			System.err.println("Problem in HelloWorld: " + e);
			e.printStackTrace();
		}
		catch(AudioException e)
		{
			System.err.println("Problem in HelloWorld: " + e);
			e.printStackTrace();
		}
		*/
		catch(Exception e)
		{
			System.err.println("Problem in HelloWorld: " + e);
			e.printStackTrace();
		}
	}
	public static void main(String[] args)
	{
		TSStoSTT tss = new TSStoSTT();
		URL url;
		String stt = "";
		try
		{
		if (args.length > 0) 
		{
		url = new File(args[0]).toURI().toURL();
		stt = tss.speechtoText(url);
		} 
		else 
		{
		url = TSStoSTT.class.getResource("helloworld.config.xml");
		stt = tss.speechtoText(url);
		}
		}
		/*
		catch(MalformedURLException e)
		{
			System.err.println("Problem in HelloWorld: " + e);
			e.printStackTrace();
		}
		*/
		catch(Exception e)
		{
			System.err.println("Problem in HelloWorld: " + e);
			e.printStackTrace();
		}
		tss.texttoSpeech(stt);
			System.out.println("LOADING COMPLETED...");
	}
}
////////////////////////////////////////////////////////////////////////////////////////////TSStoSTT.bat file////////////////////////////////////////////////////////////////////////////////////////////
javac TSStoSTT.java
java -cp .;STT_LIB/cgjsapi.jar;STT_LIB/jsapi.jar;STT_LIB/sphinx4.jar;STT_LIB/WSJ_8gau_13dCep_16k_40mel_130Hz_6800Hz.jar;TTS_LIB/cmu_time_awb.jar;TTS_LIB/cmu_us_kal.jar;TTS_LIB/cmudict04.jar;TTS_LIB/cmulex.jar;TTS_LIB/cmutimelex.jar;TTS_LIB/en_us.jar;TTS_LIB/freetts.jar;TTS_LIB/freetts-jsapi10.jar;TTS_LIB/jsapi.jar;TTS_LIB/mbrola.jar TSStoSTT
Pause
////////////////////////////////////////////////////////////////////////////////////////////helloworld.config.xml file////////////////////////////////////////////////////////////////////////////////////////////
<?xml version="1.0" encoding="UTF-8"?>

<!--
   Sphinx-4 Configuration file
-->

<!-- ******************************************************** -->
<!--  an4 configuration file                             -->
<!-- ******************************************************** -->

<config>        
    
    <!-- ******************************************************** -->
    <!-- frequently tuned properties                              -->
    <!-- ******************************************************** --> 

    <property name="logLevel" value="WARNING"/>
    
    <property name="absoluteBeamWidth"  value="-1"/>
    <property name="relativeBeamWidth"  value="1E-80"/>
    <property name="wordInsertionProbability" value="1E-36"/>
    <property name="languageWeight"     value="8"/>
    
    <property name="frontend" value="epFrontEnd"/>
    <property name="recognizer" value="recognizer"/>
    <property name="showCreations" value="false"/>
    
    
    <!-- ******************************************************** -->
    <!-- word recognizer configuration                            -->
    <!-- ******************************************************** --> 
    
    <component name="recognizer" type="edu.cmu.sphinx.recognizer.Recognizer">
        <property name="decoder" value="decoder"/>
        <propertylist name="monitors">
            <item>accuracyTracker </item>
            <item>speedTracker </item>
            <item>memoryTracker </item>
        </propertylist>
    </component>
    
    <!-- ******************************************************** -->
    <!-- The Decoder   configuration                              -->
    <!-- ******************************************************** --> 
    
    <component name="decoder" type="edu.cmu.sphinx.decoder.Decoder">
        <property name="searchManager" value="searchManager"/>
    </component>
    
    <component name="searchManager" 
        type="edu.cmu.sphinx.decoder.search.SimpleBreadthFirstSearchManager">
        <property name="logMath" value="logMath"/>
        <property name="linguist" value="flatLinguist"/>
        <property name="pruner" value="trivialPruner"/>
        <property name="scorer" value="threadedScorer"/>
        <property name="activeListFactory" value="activeList"/>
    </component>
    
    
    <component name="activeList" 
             type="edu.cmu.sphinx.decoder.search.PartitionActiveListFactory">
        <property name="logMath" value="logMath"/>
        <property name="absoluteBeamWidth" value="${absoluteBeamWidth}"/>
        <property name="relativeBeamWidth" value="${relativeBeamWidth}"/>
    </component>
    
    <component name="trivialPruner" 
                type="edu.cmu.sphinx.decoder.pruner.SimplePruner"/>
    
    <component name="threadedScorer" 
                type="edu.cmu.sphinx.decoder.scorer.ThreadedAcousticScorer">
        <property name="frontend" value="${frontend}"/>
        <property name="isCpuRelative" value="true"/>
        <property name="numThreads" value="0"/>
        <property name="minScoreablesPerThread" value="10"/>
        <property name="scoreablesKeepFeature" value="true"/>
    </component>
    
    <!-- ******************************************************** -->
    <!-- The linguist  configuration                              -->
    <!-- ******************************************************** -->
    
    <component name="flatLinguist" 
                type="edu.cmu.sphinx.linguist.flat.FlatLinguist">
        <property name="logMath" value="logMath"/>
        <property name="grammar" value="jsgfGrammar"/>
        <property name="acousticModel" value="wsj"/>
        <property name="wordInsertionProbability" 
                value="${wordInsertionProbability}"/>
        <property name="languageWeight" value="${languageWeight}"/>
        <property name="unitManager" value="unitManager"/>
    </component>
        
    
    <!-- ******************************************************** -->
    <!-- The Grammar  configuration                               -->
    <!-- ******************************************************** -->
            
    <component name="jsgfGrammar" type="edu.cmu.sphinx.jsapi.JSGFGrammar">
        <property name="dictionary" value="dictionary"/>
        <property name="grammarLocation" 
             value="grammar_Files/"/>
        <property name="grammarName" value="hello"/>
	<property name="logMath" value="logMath"/>
    </component>
    
        
    <!-- ******************************************************** -->
    <!-- The Dictionary configuration                            -->
    <!-- ******************************************************** -->
    
    <component name="dictionary" 
        type="edu.cmu.sphinx.linguist.dictionary.FastDictionary">
        <property name="dictionaryPath"
	 value="resource:/edu.cmu.sphinx.model.acoustic.WSJ_8gau_13dCep_16k_40mel_130Hz_6800Hz.Model!/edu/cmu/sphinx/model/acoustic/WSJ_8gau_13dCep_16k_40mel_130Hz_6800Hz/dict/cmudict.0.6d"/>
        <property name="fillerPath" 
	 value="resource:/edu.cmu.sphinx.model.acoustic.WSJ_8gau_13dCep_16k_40mel_130Hz_6800Hz.Model!/edu/cmu/sphinx/model/acoustic/WSJ_8gau_13dCep_16k_40mel_130Hz_6800Hz/dict/fillerdict"/>
        <property name="addSilEndingPronunciation" value="false"/>
        <property name="allowMissingWords" value="false"/>
        <property name="unitManager" value="unitManager"/>
    </component>

    <!-- ******************************************************** -->
    <!-- The acoustic model configuration                         -->
    <!-- ******************************************************** -->
    <component name="wsj" 
      type="edu.cmu.sphinx.model.acoustic.WSJ_8gau_13dCep_16k_40mel_130Hz_6800Hz.Model">
        <property name="loader" value="wsjLoader"/>
        <property name="unitManager" value="unitManager"/>
    </component>

    <component name="wsjLoader"
               type="edu.cmu.sphinx.model.acoustic.WSJ_8gau_13dCep_16k_40mel_130Hz_6800Hz.ModelLoader">
        <property name="logMath" value="logMath"/>
        <property name="unitManager" value="unitManager"/>
    </component>
    
    
    <!-- ******************************************************** -->
    <!-- The unit manager configuration                           -->
    <!-- ******************************************************** -->

    <component name="unitManager" 
        type="edu.cmu.sphinx.linguist.acoustic.UnitManager"/>

    <!-- ******************************************************** -->
    <!-- The frontend configuration                               -->
    <!-- ******************************************************** -->
    
    <component name="frontEnd" type="edu.cmu.sphinx.frontend.FrontEnd">
        <propertylist name="pipeline">
            <item>microphone </item>
            <item>premphasizer </item>
            <item>windower </item>
            <item>fft </item>
            <item>melFilterBank </item>
            <item>dct </item>
            <item>liveCMN </item>
            <item>featureExtraction </item>
        </propertylist>
    </component>
    
    <!-- ******************************************************** -->
    <!-- The live frontend configuration                          -->
    <!-- ******************************************************** -->
    <component name="epFrontEnd" type="edu.cmu.sphinx.frontend.FrontEnd">
        <propertylist name="pipeline">
            <item>microphone </item>
            <item>speechClassifier </item>
            <item>speechMarker </item>
            <item>nonSpeechDataFilter </item>
            <item>premphasizer </item>
            <item>windower </item>
            <item>fft </item>
            <item>melFilterBank </item>
            <item>dct </item>
            <item>liveCMN </item>
            <item>featureExtraction </item>
        </propertylist>
    </component>

    <!-- ******************************************************** -->
    <!-- The frontend pipelines                                   -->
    <!-- ******************************************************** -->
    
    <component name="speechClassifier" 
               type="edu.cmu.sphinx.frontend.endpoint.SpeechClassifier">
        <property name="threshold" value="13"/>
    </component>
    
    <component name="nonSpeechDataFilter" 
               type="edu.cmu.sphinx.frontend.endpoint.NonSpeechDataFilter"/>
    
    <component name="speechMarker" 
               type="edu.cmu.sphinx.frontend.endpoint.SpeechMarker" >
        <property name="speechTrailer" value="50"/>
    </component>
    
    
    <component name="premphasizer" 
               type="edu.cmu.sphinx.frontend.filter.Preemphasizer"/>
    
    <component name="windower" 
               type="edu.cmu.sphinx.frontend.window.RaisedCosineWindower">
    </component>
    
    <component name="fft" 
            type="edu.cmu.sphinx.frontend.transform.DiscreteFourierTransform">
    </component>
    
    <component name="melFilterBank" 
        type="edu.cmu.sphinx.frontend.frequencywarp.MelFrequencyFilterBank">
    </component>
    
    <component name="dct" 
            type="edu.cmu.sphinx.frontend.transform.DiscreteCosineTransform"/>
    
    <component name="liveCMN" 
               type="edu.cmu.sphinx.frontend.feature.LiveCMN"/>
        
    <component name="featureExtraction" 
               type="edu.cmu.sphinx.frontend.feature.DeltasFeatureExtractor"/>
       
    <component name="microphone" 
               type="edu.cmu.sphinx.frontend.util.Microphone">
        <property name="closeBetweenUtterances" value="false"/>
    </component>

    
    <!-- ******************************************************* -->
    <!--  monitors                                               -->
    <!-- ******************************************************* -->
    
    <component name="accuracyTracker" 
                type="edu.cmu.sphinx.instrumentation.AccuracyTracker">
        <property name="recognizer" value="${recognizer}"/>
        <property name="showAlignedResults" value="false"/>
        <property name="showRawResults" value="false"/>
    </component>
    
    <component name="memoryTracker" 
                type="edu.cmu.sphinx.instrumentation.MemoryTracker">
        <property name="recognizer" value="${recognizer}"/>
	<property name="showSummary" value="false"/>
	<property name="showDetails" value="false"/>
    </component>
    
    <component name="speedTracker" 
                type="edu.cmu.sphinx.instrumentation.SpeedTracker">
        <property name="recognizer" value="${recognizer}"/>
        <property name="frontend" value="${frontend}"/>
	<property name="showSummary" value="true"/>
	<property name="showDetails" value="false"/>
    </component>
    
    
    <!-- ******************************************************* -->
    <!--  Miscellaneous components                               -->
    <!-- ******************************************************* -->
    
    <component name="logMath" type="edu.cmu.sphinx.util.LogMath">
        <property name="logBase" value="1.0001"/>
        <property name="useAddTable" value="true"/>
    </component>
    
</config>
////////////////////////////////////////////////////////////////////////////////////////////paths  ////////////////////////////////////////////////////////////////////////////////////////////
/TTS_LIB/cmu_time_awb.jar
/TTS_LIB/cmu_us_kal.jar
/TTS_LIB/cmudict04.jar
/TTS_LIB/cmulex.jar
/TTS_LIB/cmultimelex.jar
/TTS_LIB/en_us.jar
/TTS_LIB/freetts.jar
/TTS_LIB/freetts-jsapi10.jar
/TTS_LIB/jsapi.jar
/TTS_LIB/mbrola.jar
/STT_LIB/cgjsapi.jar
/STT_LIB/jsapi.jar
/STT_LIB/sphinx4.jar
/STT_LIB/WSJ_8gau_13dCep_16k_40mel_130Hz_6800Hz.jar
/STT_LIB/cgjsapi170_x64.dll
/STT_LIB/cgjsapi170.dll
/grammar_Files/hello.gram
/cgjsapi170.dll
/helloworld.config.xml
/TSStoSTT.java
/TSStoSTT.class
/TSStoSTT.bat
